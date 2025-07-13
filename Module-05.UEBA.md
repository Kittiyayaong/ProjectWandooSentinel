# Module 05.UEBA

---

### Lab 1. UEBA 활성화하기 

1. Sentinel > Settings > 두번째 Tab Settings 클릭하여 **set UEBA**으로 UEBA 설정 확인

    <img src="https://github.com/user-attachments/assets/3aacc5af-9e70-46ac-af75-a59e77639f6e" width="1000">

2. UEBA 설정 별 항목

   <img src="https://github.com/user-attachments/assets/674c366a-4bfa-4826-9dd1-5b0058c4c9c1" width="1000">

  * Turn on the UEBA feature: On으로 되어 있어야 UEBA 기능이 활성화됨.
  > ⚠️ Microsoft Entra ID의 Global Admin 또는 Security Admin 권한을 가진 사용자만 이 기능을 켤 수 있음.

  * Directory 서비스 연동 (엔터티 프로파일 생성용): UEBA는 "누구"가 어떤 행동을 했는지를 분석하기 위해, 디렉터리 서비스와의 연결이 필요합니다. Entra ID만 체크해도 대부분의 클라우드 환경에서 UEBA는 제대로 작동합니다.
  * **Entra ID 체크 후, 하단에 Validate를 클릭하여 연동합니다.**

| 옵션                               | 설명                  | 필요 조건                                  |
| -------------------------------- | ------------------- | -------------------------------------- |
| ✅ **Active Directory (Preview)** | 온프레미스 AD 환경과 연동     | Microsoft Defender for Identity 온보딩 필요 |
| ✅ **Microsoft Entra ID**         | 클라우드 기반 Azure AD 연동 | 기본적으로 Entra ID와 연결 권장됨                 |

  *  Existing data sources 설정: UEBA에서 엔터티 행동 분석에 사용할 수 있는 기존 로그 소스 목록을 보여주며, 기존에 로그 커넥터가 아직 연동되지 않아 **No result** 상태입니다. 연동 가능한 데이터 소스는 로그들이 Sentinel에 정상적으로 수집되고 있어야, 이곳에 표시됩니다. 

     <img src="https://github.com/user-attachments/assets/1ea1e32a-a3c1-4b6e-b689-17c10d32196a" width="1000">

> ⭐ Tips.
> UEBA는 **알림(Alert)**을 바로 생성하지는 않고, 엔터티에 대한 Risk Score 증가 / 타임라인 표시로 반영됨
> Alert로 활용하려면 → Fusion / custom Analytics Rule과 결합 필요

---

## Lab 2. Risk Score 높은 사용자 활동 분석 (UEBA 타임라인)

### 목적
UEBA에서 수집된 사용자 행동 데이터를 기반으로, 수상한 사용자의 활동 타임라인을 분석하여 위협 징후를 추적

### 시나리오
회사 내부에서 로그인 실패가 급격히 늘어난 사용자가 이후 로그인에 성공하고, 이어서 권한 변경 및 SPN Key 등록을 시도한 것으로 보임.  
이 사용자가 공격자인지 UEBA 타임라인으로 추적합니다.

### 🔹 실습 절차

1. Sentinel > **Entity Behavior** 메뉴 이동  
2. 목록에서 Risk Score가 높은 사용자를 선택
3. 우측 패널에서 `Timeline` 탭 클릭
4. 타임라인을 통해 다음 항목 확인:
   - 로그인 실패 → 로그인 성공 (`SignInLogs`)
   - 역할 추가 (`RoleManagement`)
   - KeyCredential 추가 (`AuditLogs`)
   - 토큰 요청 (`TokenIssuanceLogs`)

### 참고 쿼리 (해당 사용자 추적 시)
```kql
BehaviorAnalytics
| where RiskScore > 30
| project AccountUPN, RiskScore, TimeGenerated
```

---

## Lab 3. UEBA + Analytics Rule 연계 (경고 생성)

### 목적
UEBA의 Risk Score를 기반으로 한 **고급 탐지 룰 생성**  
→ Risk Score가 높은 사용자가 민감 작업을 수행할 경우, 경고 발생

### 시나리오
Risk Score가 30 이상인 사용자가 Azure AD에서 SPN Key를 추가하면, 이를 경고(Alert)로 감지

### 실습 절차

1. Sentinel > Analytics > + Create > Scheduled rule
2. 아래 KQL 쿼리 사용:
```kql
let uebaUsers = BehaviorAnalytics
| where RiskScore > 30
| distinct AccountUPN;

AuditLogs
| extend actorUPN = tostring(parse_json(InitiatedBy_s).user.userPrincipalName)
| where actorUPN in (uebaUsers)
| where OperationName has "Add service principal"
| project TimeGenerated, actorUPN, OperationName, Result_s
```
3. Rule Logic 설정:
   - Rule name: `UEBA High Risk + SPN Key Alert`
   - Run every: 5 minutes
   - Lookup data: BehaviorAnalytics
4. Alert enrichment:
   - `AccountCustomEntity`: actorUPN


✅ 다음 Lab: [Module 05. Playbook](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/Module-06.Playbook.md)
