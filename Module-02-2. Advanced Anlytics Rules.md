# Module 2-2. Analytics Rule & Microsoft Incident Creation Rule

## 시나리오 개요

### 주제:
고위험 사용자 계정이 Azure 리소스를 연속으로 삭제하고,  
Microsoft Defender for Cloud에서 보안 경고가 발생한 경우 Sentinel이 이를 탐지하고 대응한다.

| 구분        | Lab 1: Azure Activity 탐지     | Lab 2: MDC 경고 승격                 |
| --------- | ---------------------------- | -------------------------------- |
| **탐지 방식** | Sentinel이 직접 쿼리              | Defender가 탐지 후 Sentinel이 수신      |
| **대상 로그** | Azure Activity 로그            | Defender for Cloud Alerts        |
| **활용 기술** | KQL, 엔터티 매핑, 스케줄링            | Microsoft Incident Creation Rule |
| **목표**    | 내부 위협 행위 탐지                  | 외부/시스템 기반 위협 수신 및 대응             |
| **공통점**   | Incident 생성 + 자동화 태깅으로 대응 연계 |                                  |


---

## Lab 1: Azure Activity 기반 Analytics Rule (Azure 내부에서 발생하는 관리 작업(예: 리소스 삭제)을 Sentinel이 직접 탐지)

### 목적
Azure Activity 로그에서 동일 사용자가 다수의 리소스 그룹을 삭제한 경우 탐지하여 Alert 및 Incident 생성

### 설정 경로
Microsoft Sentinel > Configuration > Analytics > + Create > Scheduled query rule

### 설정 상세

#### 1. General
- Name: Wandoo-Suspicious Resource Group Deletion
- Severity: High

#### 2. Rule Query (KQL) 
* 같은 사용자(Caller)가 같은 IP에서 리소스 그룹 삭제를 3번 이상 성공적으로 실행한 경우를 추적하는 KQL 쿼리입니다.
```kusto
AzureActivity
| where OperationNameValue == "Microsoft.Resources/subscriptions/resourcegroups/delete"
| where ActivityStatusValue == "Success"
| summarize deleteCount = count(), startTime=min(TimeGenerated), endTime=max(TimeGenerated) by Caller, CallerIpAddress
| where deleteCount >= 3
```

#### 3. Entity Mapping

| Field             | Entity Type |
|------------------|-------------|
| Caller            | Account     |
| CallerIpAddress   | IP          |

  <img width="600" alt="image" src="https://github.com/user-attachments/assets/0fabbdb0-9d2c-4a3c-b017-07dc483040fb" />

#### 4. Rule Scheduling
- Run query every: 5 minutes
- Lookup data from the last: 30 minutes

  <img width="600" alt="image" src="https://github.com/user-attachments/assets/8155496e-527f-46ab-9df6-c883eb4643f6" />

#### 5. Alert 설정 상세

* Alert Threshold
   * 최소값: 0 (조건 일치 시 무조건 Alert 생성)
     
* Event Grouping
  * 권장: “Group all events into a single alert” – 반복 이벤트를 하나로 묶어 알림 피로 줄이기
    
| 옵션                                 | 설명 |
|--------------------------------------|------|
| Group all events into a single alert | 여러 이벤트를 하나의 Alert로 묶음 (기본값, 노이즈 줄이기 유리) |
| Trigger an alert for each event      | 이벤트마다 개별 Alert 생성 (조사 필요 시 유용) |

* Suppression
  * 동일 Alert가 연속 발생 시 일정 시간 동안 경고 억제
  * 예시: Suppress alerts for 6시간 → 6시간 동안 동일 경고 다시 발생 X

#### 6. Incident 설정
  *  Incident 생성: Enabled
  * Incident Grouping (설정값)
    * Limit timeframe: 2시간
    * Group by: All matching entities
    * Re-open closed matching incidents: Enabled

> ⭐Tips. Alert들을 어떤 기준으로 묶을지 결정하는 핵심 설정*
> 
| 옵션 | 설명 | 사용 시점 |
|------|------|------------|
| **All matching entities** | 엔터티(예: 사용자, IP 등)가 모두 같을 때만 그룹핑 | 일반적인 권장값. 동일 공격자 추적 |
| **All alerts triggered by this rule** | 조건 없이 동일 룰의 모든 경고 그룹핑 | 테스트 룰, 간단한 조건일 때 |
| **Selected entity types and details match** | 사용자가 지정한 엔터티 + 필드값이 일치할 때만 그룹핑 | 고정밀 보안 환경에서 사용 |

> ⭐Tips.Re-open closed matching incidents
>
> 역할: 이미 **닫힌 인시던트가 있을 때**, 같은 조건의 Alert가 다시 발생하면 이를 기존 인시던트에 **붙일지** 여부
> 
| 설정 값 | 의미 | 사용 시점 |
|----------|------|-----------|
| **Enabled** | 기존 인시던트를 다시 열고 Alert 추가 | 동일 공격의 재시도를 하나의 흐름으로 볼 때 |
| **Disabled** | 항상 새 인시던트를 생성 | 반복 공격이 별개로 간주될 때 |


#### 7. 자동화 대응 (Automation Rule)

* 예시 구성
  * Name: TagSuspiciousUser-wandoo
  * Trigger: When incident is created
  * Condition: Severity equals High
  * Action: Add Tag → SuspiciousUser-wandoo

Logic App을 활용해 이후 이메일 알림, 계정 비활성화 등 SOAR 자동화 대응 확장 가능

---

## Lab 2: Microsoft Incident Creation Rule (Defender for Cloud에서 이미 발생한 보안 경고를 Sentinel에서 받아들여 Incident로 승격)

### 목적
Microsoft Defender for Cloud(MDC)에서 생성된 중간 또는 높은 심각도 경고를 Sentinel Incident로 승격하고 태그 자동 부여

### 설정 경로
Microsoft Sentinel > Configuration > Analytics > + Create > Microsoft incident creation rule

### 설정 상세

#### 1. General
* Name: MDC Alert Promotion for Suspicious Users

#### 2. Rule Logic
* Microsoft Security Service: Microsoft Defender for Cloud
* Severity: Custom → 선택: Medium, High

#### 3. Automated Response
* Automation Rule Name: TagHighSeverityFromMDC
* Trigger: When incident is created
* Condition: Severity equals High
* Action: Add Tag → HighSeverityFromMDC

### ✅ 다음 Lab: [Module 3.Watchlists](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/Module-03.Watchlists.md)
