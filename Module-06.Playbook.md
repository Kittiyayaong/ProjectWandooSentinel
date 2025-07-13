# Module 05. Playbook

## 목적 
Microsoft Sentinel의 Playbook (Logic App) 기능을 활용하여, 경고(Alert) 발생 시 자동 대응할 수 있는 자동화된 워크플로우 구축을 익히는 것이 목표입니다.

## Lab 정리
| 단계    | 목적                                                                   |
| ----- | -------------------------------------------------------------------- |
| Lab 1 | Logic App(Playbook) 생성 방법 학습 및 Hosting Plan 차이 이해                    |
| Lab 2 | Sentinel Analytics Rule 기반 Alert 발생 시, Teams에 자동 알림 전송하는 Playbook 구성 |

---

### Lab 1. Playbook 만들기

#### 목적
Logic App이 어떻게 만들어지는지와, 어떤 Hosting Plan을 선택해야 하는지 학습

#### 설정하기
1. Sentinel > Automation에서 신규 **Blank playbook**를 생성합니다. 

  <img src="https://github.com/user-attachments/assets/9f7be2eb-12a9-4e81-97eb-784f0e97505d" width="1000">

2. **Consumption**을 클릭합니다.
 <img src="https://github.com/user-attachments/assets/577d3c42-9e4f-4b01-9650-caf3e9a1bcc8" width="1000">

> ⭐ Consumption 정리
> 
> | 항목              | **Multi-tenant** (✅ Sentinel 추천)        | Workflow Service Plan       | App Service Environment v3 | Hybrid                |
> | --------------- | --------------------------------------- | --------------------------- | -------------------------- | --------------------- |
> | **호스팅 유형**      | Consumption 기반 (공유형)                    | 전용 워크플로 인스턴스                | 전용 App Service 환경          | 자체 관리형 (K8s 등)        |
> | **컴퓨트**         | Shared                                  | Dedicated                   | Dedicated                  | Customer managed      |
> | **네트워크**        | Public Cloud                            | VNET 연동 가능                  | VNET 연동 가능                 | 로컬 네트워크 (온프레미스 포함)    |
> | **요금**          | 사용량 기반 Pay-per-operation 💰저렴           | Plan 인스턴스 기준                | App Service 인스턴스 기준        | Kubernetes 클러스터 자원 기준 |
> | **스케일링**        | 자동 (소형 워크플로 적합)                         | 더 많은 기능, 확장성↑               | 고성능 전용 환경                  | 고급 하이브리드 시나리오         |
> | **Sentinel 연동** | ✅ 가장 일반적, Playbook에 적합                  | ❌ 과도함                       | ❌ 과도함                      | ❌ 특수 목적용              |
> | **추천 용도**       | Sentinel 자동화, 이메일/Teams 알림, 간단한 트리거 자동화 | 기업 내부 시스템 자동화 (많은 병렬 처리 필요) | 완전 격리된 고성능 환경 필요할 때        | 멀티클라우드/온프레미스 통합 필요할 때 |

3. 정보를 기입한 후, Create하여 완료합니다. 

  <img width="600" alt="image" src="https://github.com/user-attachments/assets/c82108b0-47c9-41eb-ac15-9eafb0ab441f" />

---

### Lab 2. MFA 우회 탐지 시 자동 Teams 알림 보내기

#### 목적
* Sentinel Analytics Rule 기반 탐지 설정 익히기
* 탐지된 경고를 트리거로 Logic App을 자동 실행시키고, Microsoft Teams로 대응 알림 전송하기
* Logic App에 필요한 권한 부여 및 Sentinel 연동 방법 이해

#### 설정하기
1. Analytics Rule:Unfamiliar sign-in properties 감지 룰 만들기: Sentinel > Anlytics > Creat > **Scheduled query rule**
   
 <img src="https://github.com/user-attachments/assets/0590b1db-7324-474a-9f7f-fa71691f5afe" width="600">

2. Name: Unfamiliar sign-in properties로 설정

 <img src="https://github.com/user-attachments/assets/416786b0-431d-4659-9810-d6f60625af6e" width="600">

3. 하단 Query를 넣어 진행합니다. 

 ```powershell
let threshold = 5;
let timeRange = 1d;
let mfa_signins = SigninLogs
  | where TimeGenerated >= ago(timeRange)
  | where ResultType == 0
  | where AuthenticationRequirement == "multiFactorAuthentication"
  | summarize MFA_Count = count() by UserPrincipalName;

let non_mfa_signins = SigninLogs
  | where TimeGenerated >= ago(timeRange)
  | where ResultType == 0
  | where AuthenticationRequirement != "multiFactorAuthentication"
  | summarize NonMFA_Count = count() by UserPrincipalName;

non_mfa_signins
| join kind=inner mfa_signins on UserPrincipalName
| where NonMFA_Count >= threshold
| project UserPrincipalName, NonMFA_Count, MFA_Count

 ```

* 변수 설정:
  * let threshold = 5;: 특정 기간 동안의 로그인 시도 횟수 기준을 설정합니다. 여기서는 5회 이상 로그인 시도를 기준으로 합니다.
  * let timeRange = 1d;: 쿼리가 검색할 시간 범위를 설정합니다. 여기서는 지난 1일 동안의 로그를 검색합니다.
 
* 로그인 로그 필터링:
  * SigninLogs | where TimeGenerated >= ago(timeRange): 지난 1일 동안의 로그인 로그를 필터링합니다.
  * where ResultType == 0: 성공적인 로그인 시도만 필터링합니다.
  * where AuthenticationRequirement == "multiFactorAuthentication": MFA가 요구된 로그인 시도를 필터링합니다.
  * where AuthenticationRequirement != "multiFactorAuthentication": MFA가 요구되지 않은 로그인 시도를 필터링합니다.

* 로그인 시도 요약:
  * summarize Count = count() by UserPrincipalName, IPAddress, Location: 사용자, IP 주소, 위치별로 로그인 시도 횟수를 요약합니다.
  * where Count >= threshold: 로그인 시도 횟수가 설정된 기준(threshold) 이상인 경우만 필터링합니다.

* 로그인 시도 비교:
  * join kind=inner (SigninLogs | where TimeGenerated >= ago(timeRange) | where ResultType == 0 | where AuthenticationRequirement == "multiFactorAuthentication" | summarize Count = count() by UserPrincipalName, IPAddress, Location) on UserPrincipalName: MFA가 요구된 로그인 시도와 비교하여 우회 시도를 탐지합니다.

* 결과 출력:
  * project UserPrincipalName, IPAddress, Location, Count: 사용자 이름, IP 주소, 위치, 로그인 시도 횟수를 출력합니다.
 
4. Logic App Playbook 생성: Azure portal > Logic Apps > Add > Consumtion 

   <img width="600" alt="image" src="https://github.com/user-attachments/assets/2699f9c3-c92a-47b6-b93f-da103febff3b" />

   <img src="https://github.com/user-attachments/assets/f08b8ac0-c673-4c30-94cf-bf15d9e87ef8" width="600">

> ✅ Tips

> Sentinel과 Logic App의 Region이 일치해야합니다.

5. Create해서 완료합니다. 

6. Logic app 리스트에서 새로생성된 Logic Apps를 클릭 > Development Tools > Logic app Designer > trigger 추가 > Sentinel alert를 선택

   <img src="https://github.com/user-attachments/assets/cf4bb382-b1e1-4eb6-b2ff-e5a3b32b90c8" width="600">

7. Logic App이 Sentinel Alert 발생을 트리거로 감지하기위해, Sentinel에 접근할 수 있는 권한이 필요합니다.

   <img src="https://github.com/user-attachments/assets/d35abe64-2a56-4527-9330-119268b97fcc" width="600">

   <img src="https://github.com/user-attachments/assets/6ffc62a3-3353-4323-b41f-1a8986190ba7" width="600">
  
8. Action을 추가합니다

    <img width="600" alt="image" src="https://github.com/user-attachments/assets/6251f05e-3325-4e79-931c-424493a69bf4" />

9. Save 하여 완료합니다.

10. **Logic App에 Sentinel 권한 부여**: Logic app > Identity > System assigned > Status: On으로 변경

    <img src="https://github.com/user-attachments/assets/fc58cae3-3f5f-4456-8bb1-2cae19ca759e" width="600">

11. **Sentinel Workspace에 권한 부여**: Sentinel > Access control(IAM) > + Add > Add role assignment에서 Role을 부여햡니다.
 
    <img src="https://github.com/user-attachments/assets/7235ab3f-7abb-4808-884f-ac62c3f0f47c" width="600">

> ✅ Tips.

> Logic App (Playbook)에 일반적으로 할당하는 권한은 Sentinel Responder입니다.
만약 Logic App이 리소스를 생성하거나 Sentinel 설정을 변경해야 한다면 Contributor가 필요합니다.

| 역할 이름                    | 설명                      | 주요 권한                                                   | 권한 수준     | 추천 대상                 |
| ------------------------ | ----------------------- | ------------------------------------------------------- | --------- | --------------------- |
| **Sentinel Contributor** | Sentinel 리소스 구성 및 관리 가능 | - Analytics rule 생성/편집<br>- Playbook 연결<br>- 데이터 커넥터 설정 | 전체 관리 권한  | Sentinel 관리자, 보안 아키텍트 |
| **Sentinel Responder**   | 인시던트에 대응 가능             | - 인시던트 주석/할당/종료<br>- Playbook 실행<br>- 인시던트 상태 변경        | 제한적 쓰기 권한 | SOC 분석가, 보안 담당자       |
| **Sentinel Reader**      | Sentinel 리소스 읽기 전용      | - 인시던트 보기<br>- Analytics rule 보기<br>- 데이터 열람            | 읽기 전용     | 보안 감사관, 보고용 사용자       |

12. 방금 활성화한 Logic App의 Managed Identity 선택 > Assign 합니다.

  <img width="398" height="481" alt="image" src="https://github.com/user-attachments/assets/21322d1b-e908-4dac-b41c-59c653f12205" />

13. Sentinel이 어떤 Resource Group의 Logic App들을 실행해도 되는지 지정하는 단계로 Microsoft Sentinel에서 특정 리소스 그룹(Resource Group)에 포함된 Logic App(Playbook)을 실행할 수 있도록 권한을 위임합니다.

  <img src="https://github.com/user-attachments/assets/4d36a305-6e54-4161-9aad-42a0e8a527f1" width="300">

14. Sentinel > Configuration > analytics > 기존에 생성한 **Unfamiliar sign-in properties**을 클릭합니다.
    
> ✅ Tips.

> Azure Logic App에서 만든 test용 Playbook은 Microsoft Sentinel의 Analytics rule을 통해 연결되어야 자동으로 동작합니다. Alert에 연동된 Automation rule이 실행 된 후, Playbook을 트리거하도록 연결합니다.


15. sentinel> automaion에서 **Create** 클릭해서 automation rule을 설정합니다. 
* Name: wandoo automation rule
* Trigger: Sentinel Alert 
* 생성한 Logic App을 추가 

16. Validate 후에 Save합니다.

