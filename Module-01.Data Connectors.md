# Module 01. Data Connector

## Data Connector란? 
Sentinel은 로그를 직접 수집하지 않고, Data Connector를 통해 Log Analytics Workspace에 데이터를 수집하고, 해당 데이터를 기반으로 분석, 탐지, 자동화 대응 등을 수행합니다.

### Lab 1. Azure Activity Connector

#### 목적: Azure 리소스의 활동 로그 (Activity Log) 를 Sentinel에 수집하기 ➔ 누가, 언제, 어떤 리소스를 생성/변경/삭제했는지 파악 가능

> ❗ Note: <br>
> 이번 Lab은 Azure subscription에 대한 *Reader* 이상의 권한이 필요합니다.


1. Azure portal > Sentinel > Content Hub > Azure Activity 선택 후 Install
   ![image](https://github.com/user-attachments/assets/4134ab4f-a581-4124-8ce3-9468c902e292)

2. Install 후, **Sentinel > Configuration > Data connectors**클릭 합니다. 방금 생성한 **Azure Activity** connector을 클릭한 후 **Open Connector Page**를 클릭합니다.
   ![image](https://github.com/user-attachments/assets/dc474428-743a-4213-bc31-9b9c22670f5b)

3. 우측 파트 하단에 **Launch Azure Policy Assignment Wizard**를 클릭하여 policy 생성 페이지로 이동합니다.
   ![image](https://github.com/user-attachments/assets/e2a14ae6-f96b-4dd0-9118-d24682c1eccc)

4. Basic에 **scope**를 설정합니다. 
   ![image](https://github.com/user-attachments/assets/67f2b544-a4c2-4f92-999d-a9fadc7823d4)

5. Parameter에 **Primary Log Analytics Workspace**를 설정합니다. 
   ![image](https://github.com/user-attachments/assets/b389ee15-8511-4e53-be41-1088d03f651e)

6. Remediation에 **Create a remediation task** 체크박스를 클릭힙니다.
   ![image](https://github.com/user-attachments/assets/230e2c69-6a8e-497d-ba69-c19a1c825ab6)

7. **Review and Create**를 클릭합니다.

> ⭐ Tips: <br>
> 각 Subscription에는 Azure 활동 로그(Activity logs)에 대한 최대 5개의 대상이 있습니다. 이 제한에 이미 도달한 경우, 이 연습의 일부로 만든 정책은 Microsoft Sentinel 작업 영역에 추가 대상을 추가할 수 없습니다. 이 경우 활동 로그의 진단 설정을 사용하여 이전 설정을 직접 제거할 수 있습니다. 진단 설정을 사용하여 활동 로그를 Sentinel에 직접 연결할 수도 있습니다.

8. 다시 **Sentinel > Conent Management > Content hub > Azure Activity**를 클릭하여, 우측에 **Manage**를 클릭합니다.
   ![image](https://github.com/user-attachments/assets/2f2e01f1-218c-4a98-b5ed-342053692478)

> ⭐ Tips: <br>
> **Manage view**에서 솔루션의 콘텐츠가 표시됩니다. 솔루션 팩에 포함된 모든 커넥터, 분석 규칙, 통합 문서, 헌팅 쿼리 및 기타 콘텐츠가 여기에 표시됩니다.

![image](https://github.com/user-attachments/assets/8d607bc6-30ed-4248-ba6c-a27d07923d4d)


### Lab 2. Microsoft Defender for Cloud Data Connector

#### 목적: Defender for Cloud의 보안 경고(Security Alerts)와 보안 상태(Posture)를 Sentinel에 통합

> ❗ Note: <br>
> 이번 Lab은 Azure subscription에 대한 *Security Reader* 이상의 권한이 필요합니다.
> Defender for Cloud enable해야합니다.

1. Sentinel **Content Hub**로 이동하여, *Microsoft for Cloud*를 클릭하여 Install합니다.
   ![image](https://github.com/user-attachments/assets/fb7cd705-ed57-4fe3-94c5-879b96e18390)

2. Content Hub에서 *Microsoft for Cloud*를 클릭한 후, **Manage > Subscription-based Microsoft defender for cloud**를 클릭하여, **Open connect Page**를 클릭합니다.
   ![image](https://github.com/user-attachments/assets/cf27b1fd-4a7c-4114-be02-1d5c2b1b1a61)

3. 하단의 목록에서 원하는 구독을 선택하고 연결을 클릭합니다. 작업이 완료될 때까지 기다립니다. 커넥터에 양방향 동기화를 사용하도록 설정하면 두 제품 중 하나에서 Alerts/Incidents가 다른 제품에도 반영됩니다.
   ![image](https://github.com/user-attachments/assets/56d0e6fa-e073-44f7-8c2c-94111c9b177b)

4. 하단의 목록에서 원하는 구독을 선택하고 연결을 클릭합니다. 작업이 완료될 때까지 기다립니다. 커넥터에 양방향 동기화를 사용하도록 설정하면 두 제품 중 하나에서 닫힌 알림/인시던트가 다른 제품에도 반영됩니다.


### Lab 3: Microsoft Defender Threat Intelligence connector
이번 Lab에서는 Microsoft 위협 인텔리전스 지표를 자동으로 수집하여 '위협 인텔리전스 지표' 테이블로 수집하는 **Microsoft Defender 위협 인텔리전스**([MDTI](https://learn.microsoft.com/en-us/defender/threat-intelligence/what-is-microsoft-defender-threat-intelligence-defender-ti)) 커넥터를 Sentinel 작업 영역에 추가합니다. MDTI는 추가 비용 없이 일련의 지표와 https://ti.defender.microsoft.com 포털에 대한 액세스를 제공하며, 라이선스가 필요한 MDTI 포털 및 API의 프리미엄 기능을 제공합니다.

#### 목적: Microsoft Threat Intelligence 데이터(악성 IP, 도메인, 해시 등)를 Sentinel에 수집하여 위협 탐지 및 헌팅에 활용

1. Sentinel Content hub로 이동하여, **Threat Intelligence**를 찾아 **Install**합니다.
   ![image](https://github.com/user-attachments/assets/73881b1b-4ac5-44b9-b27b-9d8eba711b46)

2. Deployment 후, **Manage** 버튼을 클릭합니다.
   ![image](https://github.com/user-attachments/assets/47a17369-b79d-46cf-ad21-f0ffc560e210)

3. *Microsoft Defender Threat Intelligence* connector를 클릭한 후, **Open Connector Page**로 이동합니다.
   ![image](https://github.com/user-attachments/assets/34a19de0-8c24-4664-8c8e-84b6cc1c2eb0)

4. Import indicator에 **All Available**로 설정하여 Connect를 진행합니다.
   ![image](https://github.com/user-attachments/assets/ac6c8b78-e2ac-4d79-8d14-87ca8eecdfc4)

5. 이 과정을 통해 위협 인텔리전스 지표가 `ThreatIntelligenceIndicator` 테이블에 수집되기 시작합니다. 



### 🔗 [다음 Lab으로 이동하기 »](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/Module-01.Data%20Connectors.md)
