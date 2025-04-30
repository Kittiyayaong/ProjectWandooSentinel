# Module 01. Data Connector

## Data Connector란? 
Microsoft Sentinel에 데이터를 수집하고 통합하기 위해 사용되는 도구입니다. 이를 통해 다양한 데이터 소스에서 데이터를 가져와 Sentinel에서 분석하고 모니터링할 수 있습니다.

### Lab 1. Azure Activity Connector

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

> ❗ Note: <br>
> 이번 Lab은 Azure subscription에 대한 *Security Reader* 이상의 권한이 필요합니다.
> Defender for Cloud enable해야합니다.

1. Sentinel **Content Hub**로 이동하여, *Microsoft for Cloud*를 클릭하여 Install합니다.
   ![image](https://github.com/user-attachments/assets/fb7cd705-ed57-4fe3-94c5-879b96e18390)

2. 

