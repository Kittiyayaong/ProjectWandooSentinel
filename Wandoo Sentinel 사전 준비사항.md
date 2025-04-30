# Module 0 - Sentinel Lab 환경 설정 

### Prerequisites

Microsoft Sentinel을 시작하려면 Microsoft Azure 구독이 있어야 합니다. 구독이 없는 경우 무료 계정을 [여기](https://azure.microsoft.com/en/free)에서 등록할 수 있습니다.

- Azure Subscription
- Azure Subscription에서 리소스 그룹을 만들 수 있는 권한
- Note: Subscription에서 처음으로 Sentinel을 설치하려면 Subscription Contribution/ Owner 권한이 필요합니다.

Microsoft Sentinel은 작업 공간에 처음 추가된 경우 처음 31일 동안 하루 최대 10GB의 데이터를 무료로 수집할 수 있는 '평가판 모드'를 제공합니다. Sentinel이 설치되면 평가판 모드임을 알리는 메시지가 표시됩니다.

### Lab 1: Microsoft Sentinel workspace

1.  [Azure Portal](http://portal.azure.com) 접속 후, 로그인
2.  Search bar에서 **Sentinel** 검색 후 **Microsoft Sentienl**클릭
   ![image](https://github.com/user-attachments/assets/f3822ef1-7e6c-4de4-a0c9-926ffc7db670)

3. Sentinel > + Create > + Create a new Workspace로 새로운 workspace를 생성
   ![image](https://github.com/user-attachments/assets/606bcb23-6949-4d76-8803-a1c4cf5df847)

4. Log Analytics Workspace 페이지에서 정보 기입 후 생성
   ![image](https://github.com/user-attachments/assets/43f647f8-8467-49f4-877b-fe7c3c10bef2)

5. 생성된 Workspace를 클릭 후 하단에 **Add**버튼을 클릭
   ![image](https://github.com/user-attachments/assets/86dd9fe6-b451-426d-b1a8-fa0df3a2e65d)

6. 배포 시작 후, 1~2분 후에 완료되면 Microsoft Sentinel Workspace 사용 준비완료


### Lab 2: Microsoft Sentinel Training Lab Solution

1. Azure portal에서, **Microsoft Sentinel Training**을 검색 후 클릭
   ![image](https://github.com/user-attachments/assets/28f17f27-553b-45ac-9a6a-01e1254b622d)

2. Create 클릭
   ![image](https://github.com/user-attachments/assets/fc39afe1-c24d-4998-9b9a-d99126a2b04b)

3. 정보 기입 후, **Review + Create** 클릭하여 생성 
   ![image](https://github.com/user-attachments/assets/f88d8b9a-37cd-4923-9df1-4a10ca7a66de)

> ⭐ Tips: <br>
> 배포 프로세스는 수집된 모든 데이터를 완료한 후 바로 사용할 수 있도록 준비되는 과정이 포함되며, 15분 정도 소요됩니다. Azure portal에서 **Sentinel**을 검색 후, List에서 생성한 Sentinel을 클릭하면, 데시보드에서 수집된 데이터와 몇 가지 최근 인시던트를 볼 수 있습니다.인시던트가 발생하는 데 몇 분 정도 걸릴 수 있습니다.

![image](https://github.com/user-attachments/assets/188650ff-d3ab-4060-b856-22aee22f4b79)

### Lab 3: Microsoft Sentinel Playbook

1. Azure portal에서 **Resource group**을 확인합니다.
   ![image](https://github.com/user-attachments/assets/60377d0c-bf2e-4308-a6e4-3c8369b17586)

2. List에서 API Connection resource, **azuresentinel-Get-GeoFromIpAndTagIncident**를 클릭합니다.
   ![image](https://github.com/user-attachments/assets/10c3915b-ef9d-4d5c-afb5-95c5ee9d8654)

3. **API Connection > General > Edit API Connection**을 클릭
   ![image](https://github.com/user-attachments/assets/a0cff192-823e-43bb-b265-72e4de1c475a)

4. **Authorize**를 클릭 후 팝업 된 로그인 정보 중 인증할 사용자를 선택
   ![image](https://github.com/user-attachments/assets/0be60d29-8c70-45cf-96d9-d162d350efa9)

5. **Save**를 클릭하여 완료
   ![image](https://github.com/user-attachments/assets/7861abf3-9d97-4f64-b832-182d46f80f02)

 
