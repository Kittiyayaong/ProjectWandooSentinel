# Module 2-2. Analytics Rule & Microsoft Incident Creation Rule

## 시나리오 개요

### 주제:
고위험 사용자 계정이 Azure 리소스를 연속으로 삭제하고,  
Microsoft Defender for Cloud에서 보안 경고가 발생한 경우 Sentinel이 이를 탐지하고 대응한다.

---

## Lab 1: Azure Activity 기반 Analytics Rule

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

## Lab 2: Microsoft Incident Creation Rule

### 목적
Microsoft Defender for Cloud(MDC)에서 생성된 중간 또는 높은 심각도 경고를 Sentinel Incident로 승격하고 태그 자동 부여

### 설정 경로
Microsoft Sentinel > Configuration > Analytics > + Create > Microsoft incident creation rule

---

### 설정 상세

#### 1. General
- Name: MDC Alert Promotion for Suspicious Users

#### 2. Rule Logic
- Microsoft Security Service: Microsoft Defender for Cloud
- Severity: Custom → 선택: Medium, High

---

#### 3. Automated Response
- Automation Rule Name: TagHighSeverityFromMDC
- Trigger: When incident is created
- Condition: Severity equals High
- Action: Add Tag → HighSeverityFromMDC

---

## 실습 흐름 요약

| 단계 | 설명 |
|------|------|
| Step 1 | Azure Activity에서 리소스 삭제 시도 탐지 (Lab A) |
| Step 2 | 동일 사용자에게 Defender for Cloud에서 보안 경고 발생 (Lab B) |
| Step 3 | Sentinel이 두 이벤트를 각각 Incident로 생성 |
| Step 4 | 자동화 룰로 각 Incident에 Tag 부착 |
| Step 5 | 필요시 Logic App 연동하여 대응 자동화 확장 가능 |

---

## 보충 설명

### Entity Mapping이란?
- Alert에서 수집한 정보(예: IP, 계정, 호스트 등)를 엔터티(Entity) 로 명시
- Incident의 시각화/헌팅/자동화에서 상관 관계를 추적하는 데 사용

공식 문서: https://learn.microsoft.com/en-us/azure/sentinel/entities-reference

---

### Incident Grouping이 중요한 이유
- 여러 Alert를 하나의 공격 흐름(Incident)으로 묶음으로써
  - 관련성 파악 향상
  - 대응 효율성 증가
  - Noise 감소 (Alert Fatigue 방지)

---

### Automation Rule vs Playbook
| 항목 | Automation Rule | Logic App Playbook |
|------|------------------|---------------------|
| 실행 조건 | Incident 생성/변경 시 | Alert, Entity 등 조건 다양 |
| 사용 목적 | 간단한 분류, 태그, 할당 | 복잡한 프로세스 자동화 (예: 이메일 발송, 티켓 생성 등) |
| 설정 위치 | Sentinel > Automation | Sentinel > Automation > Playbook 탭 |

---

## 실습 결과 기대 효과
- 실전 대응 시나리오 기반으로 Sentinel Rule 설정 이해
- Rule logic, Entity, Incident, Alert 구조의 상호 작용 실습
- SOAR 자동화 흐름까지 확장 가능

---

## 참고 자료
- https://learn.microsoft.com/en-us/azure/sentinel/tutorial-detect-threats-custom
- https://learn.microsoft.com/en-us/azure/sentinel/create-incidents-from-microsoft-alerts
- https://learn.microsoft.com/en-us/azure/azure-monitor/reference/tables/azureactivity
- https://learn.microsoft.com/en-us/azure/defender-for-cloud/alerts-overview


### 🔗 [다음 Lab으로 이동하기 »]()
