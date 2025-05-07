# Module 05. Playbook

### Lab 1. Playbook 만들기

1. Sentinel > Automation에서 신규 **Blank playbook**를 생성합니다. 

  <img src="https://github.com/user-attachments/assets/9f7be2eb-12a9-4e81-97eb-784f0e97505d" width="1000">

2. **Consumption**을 클릭합니다.
   
 <img src="https://github.com/user-attachments/assets/577d3c42-9e4f-4b01-9650-caf3e9a1bcc8" width="1000">

3. 정보를 기입한 후, Create하여 완료합니다. 

 <img src="https://github.com/user-attachments/assets/ff50cc64-e59b-4e33-94b3-911287a0ebaf" width="600">

### Lab 2. MFA 우회 탐지 시 자동 Teams 알림 보내기

 <img src="https://github.com/user-attachments/assets/8f5709e0-1668-4654-9d2a-465cbe83823a" width="600">

1. Analytics Rule:Unfamiliar sign-in properties 감지 룰 만들기: Sentinel > Anlytics > Creat > **Scheduled query rule**
   
 <img src="https://github.com/user-attachments/assets/0590b1db-7324-474a-9f7f-fa71691f5afe" width="600">

2. Name: Unfamiliar sign-in properties로 설정

 <img src="https://github.com/user-attachments/assets/416786b0-431d-4659-9810-d6f60625af6e" width="600">

3. 하단 Query를 넣어 진행합니다. 

 ```powershell
let threshold = 5;
let timeRange = 1d;
SigninLogs
| where TimeGenerated >= ago(timeRange)
| where ResultType == 0 // 성공적인 로그인 시도
| where AuthenticationRequirement == "multiFactorAuthentication"
| where AuthenticationRequirement != "multiFactorAuthentication"
| summarize Count = count() by UserPrincipalName, IPAddress, Location
| where Count >= threshold
| join kind=inner (
    SigninLogs
    | where TimeGenerated >= ago(timeRange)
    | where ResultType == 0 // 성공적인 로그인 시도
    | where AuthenticationRequirement == "multiFactorAuthentication"
    | summarize Count = count() by UserPrincipalName, IPAddress, Location
) on UserPrincipalName
| project UserPrincipalName, IPAddress, Location, Count

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

 <img src="https://github.com/user-attachments/assets/36b05e08-c8a9-4fa4-a5bf-e925f5119d72" width="600">

10. action 항목에서 Teams > Post message 항목을 추가합니다. 
 <img src="https://github.com/user-attachments/assets/36b05e08-c8a9-4fa4-a5bf-e925f5119d72" width="600">

11. Sentinel과 동일하게 Teams 로그인을 합니다. 

 <img src="https://github.com/user-attachments/assets/973c6a3d-81a1-45e8-89a3-9c1c0198ca64" width="600">

12. Teams 채널 메시지 내용을 설정합니다. 예를 들어, Alert 세부 정보를 포함합니다
 * Post As: 메시지를 게시할 주체를 선택합니다. 현재 "Flow bot"으로 설정되어 있습니다.
 * Post In: 메시지를 게시할 위치를 선택합니다. 예를 들어, 특정 Teams 채널을 선택할 수 있습니다.
 * Parameters 탭: 메시지의 내용을 설정할 수 있는 탭입니다. 여기서 Alert 세부 정보를 포함한 메시지를 구성할 수 있습니다.

   <img src="https://github.com/user-attachments/assets/8d17caa5-797a-4b1e-a777-a7b3706bc794" width="600">
   <img src="https://github.com/user-attachments/assets/8d99a8af-68a8-4b63-af93-69d00770300d" width="600">

13. Save 하여 완료합니다.

  <img src="https://github.com/user-attachments/assets/b84527ff-beab-4772-9461-82b05811ae07" width="600">

14. **Logic App에 Sentinel 권한 부여**: Logic app > Identity > System assigned > Status: On으로 변경

  <img src="https://github.com/user-attachments/assets/fc58cae3-3f5f-4456-8bb1-2cae19ca759e" width="600">

15. **Sentinel Workspace에 권한 부여**:  Sentinel > Settings > Workspace settings > Access control(IAM) > + Add > Add role assignment

  <img src="https://github.com/user-attachments/assets/62b6e69b-2b56-4b0e-a196-67407962207f" width="600">

16. Role을 부여햡니다.
 
  <img src="https://github.com/user-attachments/assets/7235ab3f-7abb-4808-884f-ac62c3f0f47c" width="600">

> ✅ Tips.

> Logic App (Playbook)에 일반적으로 할당하는 권한은 Sentinel Responder입니다.
만약 Logic App이 리소스를 생성하거나 Sentinel 설정을 변경해야 한다면 Contributor가 필요합니다.

| 역할 이름                    | 설명                      | 주요 권한                                                   | 권한 수준     | 추천 대상                 |
| ------------------------ | ----------------------- | ------------------------------------------------------- | --------- | --------------------- |
| **Sentinel Contributor** | Sentinel 리소스 구성 및 관리 가능 | - Analytics rule 생성/편집<br>- Playbook 연결<br>- 데이터 커넥터 설정 | 전체 관리 권한  | Sentinel 관리자, 보안 아키텍트 |
| **Sentinel Responder**   | 인시던트에 대응 가능             | - 인시던트 주석/할당/종료<br>- Playbook 실행<br>- 인시던트 상태 변경        | 제한적 쓰기 권한 | SOC 분석가, 보안 담당자       |
| **Sentinel Reader**      | Sentinel 리소스 읽기 전용      | - 인시던트 보기<br>- Analytics rule 보기<br>- 데이터 열람            | 읽기 전용     | 보안 감사관, 보고용 사용자       |

17. 방금 활성화한 Logic App의 Managed Identity 선택 > Assign 합니다.

  <img src="https://github.com/user-attachments/assets/a9309694-b3c2-4ae1-a8ee-67a74f88448a" width="600">

18. Sentinel이 어떤 Resource Group의 Logic App들을 실행해도 되는지 지정하는 단계로 Microsoft Sentinel에서 특정 리소스 그룹(Resource Group)에 포함된 Logic App(Playbook)을 실행할 수 있도록 권한을 위임합니다.

  <img src="https://github.com/user-attachments/assets/4d36a305-6e54-4161-9aad-42a0e8a527f1" width="300">

17. Sentinel > Configuration > analytics > 기존에 생성한 **Unfamiliar sign-in properties**을 클릭한다.
    
> ✅ Tips.

> Azure Logic App에서 만든 test용 Playbook은 Microsoft Sentinel의 Analytics rule을 통해 연결되어야 자동으로 동작합니다. Alert에 연동된 Automation rule이 실행 된 후, Playbook을 트리거하도록 연결합니다.

  <img src="https://github.com/user-attachments/assets/f473ca44-202c-499b-8005-ab2bfa0034db" width="600">

18. **Automated response**에서 **Add new**를 클릭해서 automation rule을 설정합니다. name 설정 후, Sentinel Alert / Logic App을 추가하여 설정 완료합니다. 

  <img src="https://github.com/user-attachments/assets/10ac09d6-9276-4bae-b656-98579e4e9fac" width="600">

19. Validate 후에 Save합니다.

