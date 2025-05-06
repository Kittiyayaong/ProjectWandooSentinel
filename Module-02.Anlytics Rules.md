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
  
   ![image](https://github.com/user-attachments/assets/ab561db7-0bee-40da-a109-ef24617a4792)

4. **Analytics Rule Wizard**의 첫 페이지에서 규칙이 사용됨으로 설정되어 있는지 확인한 후, **Set rule logic**을 클릭합니다.

  ![image](https://github.com/user-attachments/assets/d39c306a-c3a0-48ac-8b1a-50ceab4baa7e)

5. **Set rule logic**설정 화면에서는 KQL 쿼리 생성 또는 수정, 엔티티 매핑 제어, 알림 그룹화 활성화 및 조정, 스케줄링 및 룩백 시간 범위 정의 등의 기능을 사용할 수 있습니다.

 * Rule Query (규칙 쿼리): 디자인, 빌드 및 테스트한 쿼리를 규칙 쿼리 창에 붙여넣습니다. 이 창에서 변경하는 모든 사항은 즉시 유효성이 검사되므로 실수가 있는 경우 창 바로 아래에 표시가 나타납니다. 이번 랩에서는 Default로 지정된 Rule query를 그대로 사용합니다.
 
 * Alert Enhancement의 **Entity Mapping**: 단일 분석 규칙에서 최대 10개의 엔터티 매핑을 정의할 수 있습니다. 동일한 형식의 엔터티를 둘 이상 매핑할 수도 있습니다. 예를 들어 ‘원본 IP 주소’ 필드와 ‘대상 IP 주소’ 필드에서 하나씩, 두 개의 IP 엔터티를 매핑할 수 있습니다. 이렇게 하면 두 엔터티를 모두 추적할 수 있습니다.  하단에 **+ Add new entity**를 클릭한 후, 기존 리스트업 되어있는 Entity를 확인합니다. 추가 할 경우, 드롭다운 목록에서 entity를 선택합니다. 사용할 수 있는 전체 엔터티 형식은 [여기](https://learn.microsoft.com/en-us/azure/sentinel/entities-reference)를 참조하세요.

   ![image](https://github.com/user-attachments/assets/245ae73b-8801-43bf-96ac-a64946c605ef)
 
 * **Query Scheduling**
   * **Run query every**: 쿼리 간격( 쿼리 실행 빈도)을 제어합니다. 허용 범위는 5분~14일 입니다.
   * **Lookup data from the last**: 조회 기간( 쿼리에서 다루는 기간)을 결정합니다. 허용 범위는 동일하게 5분~14일로 지정가능합니다. **run quqery every** 간격보다 길거나 같아야 합니다.
   * **Start running**: 규칙 생성(또는 사용) 시간 후 10분 에서 30일 후 까지로 일정 지정할 수 있습니다.
      * Automatically: 규칙이 생성되는 즉시 처음으로 실행되고 그 후에 쿼리 간격으로 실행됩니다.
      * At Specific time: 규칙이 처음 실행될 날짜와 시간을 설정한 후 쿼리 간격으로 실행됩니다.

  ![image](https://github.com/user-attachments/assets/81a2b828-61d8-4ba3-95a9-3fd1a8e1d097)

* **Alert threshold(경고 임계값)**: 경고 임계값 섹션을 사용하여 규칙의 민감도 수준을 정의합니다. 예를 들어 최소 임계값을 100으로 설정합니다. 임계값을 설정하지 않으려면 숫자 필드에 0을 입력합니다.
  ![image](https://github.com/user-attachments/assets/282bfebb-2ccb-4aa5-8a06-c758f4233b88)

* **Event grouping(이벤트 그룹화 설정)**: Event Grouping은 같은 보안 이벤트나 관련 이벤트들을 논리적으로 묶어서 하나의 incident 또는 alert로 처리하기 위해 사용됩니다. 
    * Group all events into a single alert(Default):쿼리가 위의 지정된 경고 임계값보다 더 많은 결과를 반환하는 한 규칙은 실행될 때마다 단일 경고를 생성합니다. 이 단일 경고에는 쿼리 결과에서 반환된 모든 이벤트가 요약되어 있습니다.
    * Trigger an alert for each event: 쿼리 결과의 각 레코드마다 개별 알림(Alert)을 생성하고 싶을 때 사용합니다. 예로, 각 악성 IP 탐지마다 별도 경고 생성할 수 있습니다.

> ⭐ Tips: <br>

| 상황                             | 사용 권장 여부                 |
|----------------------------------|--------------------------------|
| 개별 이벤트마다 조사 필요        | ✅ Trigger an alert for each event                          |
| 반복 이벤트를 묶는 게 더 효율적일 때 | ✅ Group all events into a single alert                      |


* **Suppression**: 경고가 생성된 후 규칙을 일시적으로 비활성화합니다. 경고가 생성되는 경우 다음 실행 시간 이후에 규칙을 억제하려면 경고 생성 후 쿼리 실행 중지 설정을 켜기로 설정합니다. 이 기능을 켜면 쿼리 실행을 중지 해야 하는 시간(최대 24시간)으로 쿼리 실행 중지를 설정합니다.

    ![image](https://github.com/user-attachments/assets/44dfe0f4-f931-4932-a53b-33da8e1106a7)

6. **Incident settings**: 인시던트 설정 탭에서 Microsoft Sentinel이 경고를 실행 가능한 인시던트로 전환할지 여부와 경고가 인시던트에서 함께 그룹화되는지 여부 및 방법을 선택합니다.
  * INcident setting: 인시던트 설정 섹션에서 이 분석 규칙에 의해 트리거된 경고로부터 인시던트 생성이 기본적으로 Enabled로 설정됩니다. 즉, Microsoft Sentinel은 규칙에 의해 트리거되는 각 경고에서 단일 인시던트를 따로 만듭니다.
   ![image](https://github.com/user-attachments/assets/4d74434a-a0bf-4f88-9e62-2d176d718e55)

  * **Alert grouping(경고 그룹화 설정)**: 경고 그룹화 섹션에서 최대 150개의 유사 또는 반복 경고 그룹에서 단일 인시던트를 생성하려면, 이 분석 규칙에 의해 트리거된 관련 경고를 인시던트로 그룹화를 사용으로 설정한 후, 하단에 매개변수를 추가 설정합니다. 
    ![image](https://github.com/user-attachments/assets/3c0d3ebf-38b3-4db9-a83a-6cde49f227fe)

   * 매개변수 1. Limit the group to alerts created within the selected time frame: 그룹을 선택한 시간 프레임 내에서 만든 경고로 제한합니다. 유사하거나 되풀이되는 경고가 함께 그룹화되는 시간 프레임을 설정합니다. 이 시간 프레임을 벗어난 경고는 별도의 인시던트 또는 인시던트 집합을 생성합니다.
   * 매개변수 2. Group alerts triggered by this analytics rule into a single incident by: 분석 규칙에 의해 트리거된 경고를 단일 인시던트에 의해 그룹화합니다. 경고가 함께 그룹화되는 방법을 선택합니다.
     | 옵션                                                       | 설명                                                                                                                                     |
|------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 모든 엔터티가 일치하는 경우 경고를 단일 인시던트로 그룹화    | 경고는 매핑된 각 엔터티에 대해 동일한 값을 공유하는 경우 함께 그룹화됩니다 (위의 `규칙 설정 논리` 탭에 정의됨). 권장 설정입니다.             |
| 이 규칙에 의해 트리거된 모든 경고를 단일 인시던트로 그룹화   | 이 규칙에 의해 생성된 모든 경고가 동일한 값을 공유하지 않더라도 함께 그룹화됩니다.                                                     |
| 선택한 엔터티 및 세부 정보가 일치하는 경우 경고를 단일 인시던트로 그룹화 | 경고는 매핑된 모든 엔터티, 경고 세부 정보 및 해당 드롭다운 목록에서 선택한 사용자 지정 세부 정보의 동일한 값을 공유하는 경우 함께 그룹화됩니다. |

  * 매개변수 3. Re-open closed matching incidents: 인시던트가 해결되고 Close으며 나중에 해당 인시던트에 속해야 하는 다른 경고가 생성된 경우 닫힌 인시던트를 다시 열려면 이 설정을 Enabled 로 설정하고 경고에서 새 인시던트를 만들려면 **Disabled**로 설정니다.
    
