# Module 05.UEBA

### Lab 1. UEBA 활성화하기 

1. Sentinel > Settings > 두번째 Tab Settings 클릭하여 UEBA 설정 확인

  <img src="https://github.com/user-attachments/assets/3aacc5af-9e70-46ac-af75-a59e77639f6e" width="1000">

2. UEBA 설정 별 항목

   <img src="https://github.com/user-attachments/assets/674c366a-4bfa-4826-9dd1-5b0058c4c9c1" width="1000">

  * Turn on the UEBA feature: On으로 되어 있어야 UEBA 기능이 활성화됨.
  > ⚠️ Microsoft Entra ID(구 Azure AD)의 Global Admin 또는 Security Admin 권한을 가진 사용자만 이 기능을 켤 수 있음.

  * Directory 서비스 연동 (엔터티 프로파일 생성용): UEBA는 "누구"가 어떤 행동을 했는지를 분석하기 위해, 디렉터리 서비스와의 연결이 필요합니다. Entra ID만 체크해도 대부분의 클라우드 환경에서 UEBA는 제대로 작동합니다.
    Entra ID 체크 후, 하단에 Validate를 클릭하여 연동합니다.

| 옵션                               | 설명                  | 필요 조건                                  |
| -------------------------------- | ------------------- | -------------------------------------- |
| ✅ **Active Directory (Preview)** | 온프레미스 AD 환경과 연동     | Microsoft Defender for Identity 온보딩 필요 |
| ✅ **Microsoft Entra ID**         | 클라우드 기반 Azure AD 연동 | 기본적으로 Entra ID와 연결 권장됨                 |

  *  Existing data sources 설정: UEBA에서 엔터티 행동 분석에 사용할 수 있는 기존 로그 소스 목록을 보여주며, 기존에 로그 커넥터가 아직 연동되지 않아 **No result** 상태입니다. 연동 가능한 데이터 소스는 로그들이 Sentinel에 정상적으로 수집되고 있어야, 이곳에 표시됩니다. 

     <img src="https://github.com/user-attachments/assets/1ea1e32a-a3c1-4b6e-b689-17c10d32196a" width="1000">

>  ✅ Tips
> UEBA는 **알림(Alert)**을 바로 생성하지는 않고, 엔터티에 대한 Risk Score 증가 / 타임라인 표시로 반영됨
> Alert로 활용하려면 → Fusion / custom Analytics Rule과 결합 필요

