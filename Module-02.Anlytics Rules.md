# Module 2. Analytics Rules

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

2. **+ add filter**를 클릭하여, **Source name**을 *Azure Activity*로 필터를 생성합니다.
   ![image](https://github.com/user-attachments/assets/b6cd194d-63c0-46c6-b7af-2726d1cbf09f)

3.  *Suspicious Resource deployment* 템플릿을 찾아, 우측 (...)을 클릭하고, 오른쪽 패널에서 간단한 요약 설명을 확인합니다. Default parameter를 그대로 사용하여 **Create Rule**를 클릭합니다.
  
 ![image](https://github.com/user-attachments/assets/d39c306a-c3a0-48ac-8b1a-50ceab4baa7e)

4. **Analytics Rule Wizard**의 첫 페이지에서 규칙이 사용됨으로 설정되어 있는지 확인한 후, **Set rule logic**을 클릭합니다.

  ![image](https://github.com/user-attachments/assets/0c781af2-b9ef-42f5-8b12-1ddbf7dce2fe)


5. **Set rule logic**설정 화면에서는 KQL 쿼리 생성 또는 수정, 엔티티 매핑 제어, 알림 그룹화 활성화 및 조정, 스케줄링 및 룩백 시간 범위 정의 등의 기능을 사용할 수 있습니다.

 * Rule Query (규칙 쿼리): 디자인, 빌드 및 테스트한 쿼리를 규칙 쿼리 창에 붙여넣습니다. 이 창에서 변경하는 모든 사항은 즉시 유효성이 검사되므로 실수가 있는 경우 창 바로 아래에 표시가 나타납니다. 이번 랩에서는 Default로 지정된 Rule query를 그대로 사용합니다.

6. Alert Enhancement의 **Entity Mapping**: 단일 분석 규칙에서 최대 10개의 엔터티 매핑을 정의할 수 있습니다. 동일한 형식의 엔터티를 둘 이상 매핑할 수도 있습니다. 예를 들어 ‘원본 IP 주소’ 필드와 ‘대상 IP 주소’ 필드에서 하나씩, 두 개의 IP 엔터티를 매핑할 수 있습니다. 이렇게 하면 두 엔터티를 모두 추적할 수 있습니다.

   ![image](https://github.com/user-attachments/assets/245ae73b-8801-43bf-96ac-a64946c605ef)
 
7. 하단에 **+ Add new entity**를 클릭한 후, Entity 드롭다운 목록에서 entity를 선택하비다. 
   ![image](https://github.com/user-attachments/assets/728aa722-1bd5-4006-a762-eef757ba36a6)

