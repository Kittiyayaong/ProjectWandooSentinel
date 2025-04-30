![image](https://github.com/user-attachments/assets/e28895ea-a804-4da2-8e66-de1c6aa94e53)# Module 2. Analytics Rules

## Anlytics Rule이란?
Sentinel analytic rule은 Microsoft Sentinel에서 보안 위협을 탐지하고 대응하기 위해 사용하는 규칙입니다. 이 규칙은 로그 데이터를 분석하여 의심스러운 활동을 식별하고, 이를 기반으로 경고를 생성합니다. Sentinel analytic rule은 다양한 유형의 규칙을 포함하며, 사용자 정의 규칙을 생성할 수도 있습니다

* Sentinel analytic rule에서 제공하는 기능
   *	로그 분석: Kusto Query Language(KQL)를 사용하여 로그 데이터를 분석하고, 특정 패턴이나 이상 징후를 탐지합니다.
   *	경고 생성: 탐지된 위협에 대해 경고를 생성하여 보안 팀이 신속하게 대응할 수 있도록 합니다.
   *	자동화된 대응: 경고에 대한 자동화된 대응을 설정하여, 반복적인 작업을 줄이고 대응 시간을 단축할 수 있습니다.
   *	사용자 정의 규칙: 조직의 특정 요구에 맞게 사용자 정의 규칙을 생성할 수 있습니다.

### Lab 1. Azure Activity rule 활성화 

1. **Sentinel > Configuration > Analytics**로 이동하여 중간 탭인 **Rule templates**를 클릭합니다. 
   ![image](https://github.com/user-attachments/assets/0f47d76b-aa37-46d9-a265-28c2b8c7367a)

> ⭐ Tips: <br>
> 규칙 템플릿(Rule template)은 콘텐츠 허브에 의해 여기에 설치됩니다. 여기에 제공된 템플릿을 기반으로 활성 규칙을 만들거나 처음부터 새로 만들 수 있습니다.

2. **+ add filter**를 클릭하여, Source name을 *Azure Activity*로 필터를 생성합니다.
   ![image](https://github.com/user-attachments/assets/b6cd194d-63c0-46c6-b7af-2726d1cbf09f)

3.  *Suspicious Resource deployment* 템플릿을 찾아, 우측 (...)을 클릭하고, 오른쪽 패널에서 간단한 요약 설명을 확인합니다. Default parameter를 그대로 사용하여 **Create Rule**를 클릭합니다.
   ![image](https://github.com/user-attachments/assets/a17617c2-9e69-4d9d-a0e6-7a43b3c8ff8a)

   - Default *Severity*는 낮음으로 지정되어 있습니다.
   - *Description*은 규칙의 의도(intent)를 설명합니다.
   - 이 규칙은 **Azure Activity** 데이터 원본을 사용하며, 녹색 “연결됨” 아이콘이 표시되어 `AzureActivity` 테이블을 사용할 수 있음을 나타냅니다.
   - 템플릿은 위협 분류 및 보고를 위한 *Tactic*의 *Impact*을 지정합니다.
   - 빠른 검사를 위해 탐지를 실행하는 *KQL 쿼리*의 미리 보기가 *Rule Query* 영역에 표시됩니다.

   
