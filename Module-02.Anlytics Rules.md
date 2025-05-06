![image](https://github.com/user-attachments/assets/20769cd1-5c37-44dc-9b2d-dd58dde5e62f)# Module 2. Analytics Rules

## Anlytics Rule이란?
Sentinel analytic rule은 Microsoft Sentinel에서 보안 위협을 탐지하고 대응하기 위해 사용하는 규칙입니다. 이 규칙은 로그 데이터를 분석하여 의심스러운 활동을 식별하고, 이를 기반으로 경고를 생성합니다. Sentinel analytic rule은 다양한 유형의 규칙을 포함하며, 사용자 정의 규칙을 생성할 수도 있습니다

* Sentinel analytic rule에서 제공하는 기능
   *	로그 분석: Kusto Query Language(KQL)를 사용하여 로그 데이터를 분석하고, 특정 패턴이나 이상 징후를 탐지합니다.
   *	경고 생성: 탐지된 위협에 대해 경고를 생성하여 보안 팀이 신속하게 대응할 수 있도록 합니다.
   *	자동화된 대응: 경고에 대한 자동화된 대응을 설정하여, 반복적인 작업을 줄이고 대응 시간을 단축할 수 있습니다.
   *	사용자 정의 규칙: 조직의 특정 요구에 맞게 사용자 정의 규칙을 생성할 수 있습니다.

### Lab 1. Azure Activity rule 활성화 

1. **Sentinel > Configuration > Analytics**로 이동하여 중간 탭인 **Rule templates**를 클릭합니다.
   
  <img src="https://github.com/user-attachments/assets/0f47d76b-aa37-46d9-a265-28c2b8c7367a" width="600"/>

2. **+ add filter**를 클릭하여, **Source name**을 *Azure Activity*로 필터를 생성합니다.
   <img src="https://github.com/user-attachments/assets/b6cd194d-63c0-46c6-b7af-2726d1cbf09f" width="600"/>

4.  *Suspicious Resource deployment* 템플릿을 찾아, 우측 (...)을 클릭하고, 오른쪽 패널에서 간단한 요약 설명을 확인합니다. Default parameter를 그대로 사용하여 **Create Rule**를 클릭합니다.
 
   <img src="https://github.com/user-attachments/assets/ab561db7-0bee-40da-a109-ef24617a4792" width="600"/>

5. **Analytics Rule Wizard**의 첫 페이지에서 규칙이 사용됨으로 설정되어 있는지 확인한 후, **Set rule logic**을 클릭합니다.

  <img src="https://github.com/user-attachments/assets/d39c306a-c3a0-48ac-8b1a-50ceab4baa7e" width="600"/>

6. **Set rule logic**설정 화면에서는 KQL 쿼리 생성 또는 수정, 엔티티 매핑 제어, 알림 그룹화 활성화 및 조정, 스케줄링 및 룩백 시간 범위 정의 등의 기능을 사용할 수 있습니다.

 * Rule Query (규칙 쿼리): 디자인, 빌드 및 테스트한 쿼리를 규칙 쿼리 창에 붙여넣습니다. 이 창에서 변경하는 모든 사항은 즉시 유효성이 검사되므로 실수가 있는 경우 창 바로 아래에 표시가 나타납니다. 이번 랩에서는 Default로 지정된 Rule query를 그대로 사용합니다.
 
 * Alert Enhancement의 **Entity Mapping**: 단일 분석 규칙에서 최대 10개의 엔터티 매핑을 정의할 수 있습니다. 동일한 형식의 엔터티를 둘 이상 매핑할 수도 있습니다. 예를 들어 ‘원본 IP 주소’ 필드와 ‘대상 IP 주소’ 필드에서 하나씩, 두 개의 IP 엔터티를 매핑할 수 있습니다. 이렇게 하면 두 엔터티를 모두 추적할 수 있습니다.  하단에 **+ Add new entity**를 클릭한 후, 기존 리스트업 되어있는 Entity를 확인합니다. 추가 할 경우, 드롭다운 목록에서 entity를 선택합니다. 사용할 수 있는 전체 엔터티 형식은 [여기](https://learn.microsoft.com/en-us/azure/sentinel/entities-reference)를 참조하세요.

   <img src="https://github.com/user-attachments/assets/245ae73b-8801-43bf-96ac-a64946c605ef" width="600"/>

 * **Query Scheduling**
   * **Run query every**: 쿼리 간격( 쿼리 실행 빈도)을 제어합니다. 허용 범위는 5분~14일 입니다.
   * **Lookup data from the last**: 조회 기간( 쿼리에서 다루는 기간)을 결정합니다. 허용 범위는 동일하게 5분~14일로 지정가능합니다. **run quqery every** 간격보다 길거나 같아야 합니다.
   * **Start running**: 규칙 생성(또는 사용) 시간 후 10분 에서 30일 후 까지로 일정 지정할 수 있습니다.
      * Automatically: 규칙이 생성되는 즉시 처음으로 실행되고 그 후에 쿼리 간격으로 실행됩니다.
      * At Specific time: 규칙이 처음 실행될 날짜와 시간을 설정한 후 쿼리 간격으로 실행됩니다.

   <img src="https://github.com/user-attachments/assets/81a2b828-61d8-4ba3-95a9-3fd1a8e1d097" width="600"/>

* **Alert threshold(경고 임계값)**: 경고 임계값 섹션을 사용하여 규칙의 민감도 수준을 정의합니다. 예를 들어 최소 임계값을 100으로 설정합니다. 임계값을 설정하지 않으려면 숫자 필드에 0을 입력합니다.

   <img src="https://github.com/user-attachments/assets/282bfebb-2ccb-4aa5-8a06-c758f4233b88" width="600"/>

* **Event grouping(이벤트 그룹화 설정)**: Event Grouping은 같은 보안 이벤트나 관련 이벤트들을 논리적으로 묶어서 하나의 incident 또는 alert로 처리하기 위해 사용됩니다. 
    * Group all events into a single alert(Default):쿼리가 위의 지정된 경고 임계값보다 더 많은 결과를 반환하는 한 규칙은 실행될 때마다 단일 경고를 생성합니다. 이 단일 경고에는 쿼리 결과에서 반환된 모든 이벤트가 요약되어 있습니다.
    * Trigger an alert for each event: 쿼리 결과의 각 레코드마다 개별 알림(Alert)을 생성하고 싶을 때 사용합니다. 예로, 각 악성 IP 탐지마다 별도 경고 생성할 수 있습니다.

| 상황                             | 사용 권장 여부                 |
|----------------------------------|--------------------------------|
| 개별 이벤트마다 조사 필요        | ✅ Trigger an alert for each event                          |
| 반복 이벤트를 묶는 게 더 효율적일 때 | ✅ Group all events into a single alert                      |


* **Suppression**: 경고가 생성된 후 규칙을 일시적으로 비활성화합니다. 경고가 생성되는 경우 다음 실행 시간 이후에 규칙을 억제하려면 경고 생성 후 쿼리 실행 중지 설정을 켜기로 설정합니다. 이 기능을 켜면 쿼리 실행을 중지 해야 하는 시간(최대 24시간)으로 쿼리 실행 중지를 설정합니다.

  <img src="https://github.com/user-attachments/assets/44dfe0f4-f931-4932-a53b-33da8e1106a7" width="600"/>

6. **Incident settings**: 인시던트 설정 탭에서 Microsoft Sentinel이 경고를 실행 가능한 인시던트로 전환할지 여부와 경고가 인시던트에서 함께 그룹화되는지 여부 및 방법을 선택합니다.
  * Incident setting: 인시던트 설정 섹션에서 이 분석 규칙에 의해 트리거된 경고로부터 인시던트 생성이 기본적으로 Enabled로 설정됩니다. 즉, Microsoft Sentinel은 규칙에 의해 트리거되는 각 경고에서 단일 인시던트를 따로 만듭니다.

  <img src="https://github.com/user-attachments/assets/4d74434a-a0bf-4f88-9e62-2d176d718e55" width="600"/>

  * **Alert grouping(경고 그룹화 설정)**: 경고 그룹화 섹션에서 최대 150개의 유사 또는 반복 경고 그룹에서 단일 인시던트를 생성하려면, 이 분석 규칙에 의해 트리거된 관련 경고를 인시던트로 그룹화를 사용으로 설정한 후, 하단에 매개변수를 추가 설정합니다. 

  <img src="https://github.com/user-attachments/assets/3c0d3ebf-38b3-4db9-a83a-6cde49f227fe" width="600"/>

✅ **매개 변수 요약**

| 매개변수        | 핵심 목적                 |
| ----------- | --------------------- |
| 시간 프레임 제한   | 연관성 있는 이벤트만 묶기 위해     |
| 그룹 기준 지정    | 공격 흐름에 맞춰 정확히 묶기 위해   |
| 닫힌 인시던트 재오픈 | 반복 공격 시 대응 흐름 유지하기 위해 |

   * 매개변수 1. Limit the group to alerts created within the selected time frame:그룹핑할 경고의 시간 범위를 제한
     * 사용 이유?: 공격이 장기간에 걸쳐 발생할 수도 있지만, 대부분의 경우 짧은 시간 내 연속적인 이벤트가 연관된 공격일 가능성이 큽니다. 이 설정은 시간 프레임을 기준으로 관련 없는 이전 이벤트와의 불필요한 그룹핑을 방지합니다.
     * 예시: 1시간 안에 발생한 로그인 실패 시도는 같은 인시던트로 묶되, 3일 전 경고는 제외.
       
   * 매개변수 2. Group alerts triggered by this analytics rule into a single incident by: : 어떤 기준으로 Alert를 묶어 Incident로 만들지 지정
      * 사용 이유?: 경고 간의 관련성을 기준으로 적절하게 묶어야 공격 흐름을 놓치지 않고, 불필요한 인시던트 증가도 방지할 수 있음.
      * 선택 옵션 및 사용 목적 
     
  | 옵션 | 설명 |
|------|------|
| 모든 엔터티가 일치 | 같은 사용자, IP 등 식별자 기준으로 그룹. 관련성 높은 경고만 묶고 싶을 때 사용 (정밀한 그룹핑). 가장 일반적이며 권장됨. |
| 모든 경고를 묶음 | 조건 상관없이 같은 룰에서 나온 모든 경고를 묶음. 테스트나 단순 시나리오, 혹은 관련성 파악이 어려울 때 사용. |
| 선택한 엔터티/세부 정보가 일치화 | 엔터티뿐 아니라 추가 필드 값까지 조건으로 설정해 묶음. 높은 정밀도 요구 시 사용 (예: 사용자 + 위치 + 디바이스 정보 등). |

  * 매개변수 3. Re-open closed matching incidents: 해결되고 Close된 인시던트에 새 경고를 붙일지 여부
    * 사용 이유?: 이미 종료된 인시던트와 같은 유형의 경고가 다시 발생하면, 새로 만드는 게 나을지, 기존 인시던트를 다시 열고 이어붙이는 게 나을지 결정할 수 있음
    * 예시: 공격자가 재시도한 흔적이 포착됐을 때 이전 공격의 연장선으로 간주하고 기존 인시던트를 재활용할 수 있음 → 대응 흐름 유지.

| 설정           | 의미                                          |
| ------------ | ------------------------------------------- |
| **Enabled**  | 조건 일치 시 닫힌 인시던트를 다시 열고 새 경고를 붙임 (공격 연속성 강조) |
| **Disabled** | 항상 새 인시던트 생성 (새로운 시도로 간주)                   |  

> ❓ Alert Grouping 기능 사용 이유
> 1. 동일한 공격이 여러 개의 경고를 생성하기 때문입니다.
>    - 사이버 공격은 한 번에 끝나지 않습니다.
>    - 포트 스캔 → 로그인 시도 → 권한 상승 → 악성 파일 실행으로 이어지면 각각 **경고(Alert)** 로 생성됩니다. → 이걸 하나하나 따로 대응하면 **맥락을 놓치고 비효율적**입니다.
> 2. 경고 폭주(Noise) 줄이기 위해
>    - 예를 들어 실패한 로그인 시도가 100번 발생하면 Alert가 100개 생성될 수도 있음
>    → SOC 팀이 대응 불가능할 정도로 **경고가 쏟아짐 (Alert Fatigue)**
> 3. 자동화 대응 (SOAR)에 적합한 단위 제공
>    - Logic App 같은 자동 대응 도구는 **Incident 단위**로 작동합니다.
>    - 관련 Alert를 그룹화하지 않으면 **부분적인 반응만 일어나고 전체 공격을 놓칠 수 있음**

> ✅ **해결책**: 관련된 여러 Alert를 **자동으로 묶어서 하나의 Incident로 그룹핑**  
> → 공격의 "전개 흐름"을 한 눈에 보고 대응 가능

> 🧷 비유하자면..
>   - 개별 Alert = CCTV에서 찍힌 사진 한 장
>   - Grouping된 Incident = 침입범의 동선을 담은 **전체 영상 클립**


7. 설정 완료 후 Save하여 생성합니다.

   <img src="https://github.com/user-attachments/assets/704a50e9-fc55-40b8-a7bb-c8b3d8680f35" width="600"/>

---

### Lab 2. Microsoft Defender for Cloud에 대한 Microsoft 인시던트 만들기 규칙 

Microsoft Sentinel은 클라우드 네이티브 SIEM이므로 경고 및 이벤트 상관 관계를 위한 단일 창 역할을 합니다. 이를 위해 Microsoft 보안 제품에서 경고를 수집하고 표시할 수 있도록 Microsoft 인시던트 만들기 규칙을 만들 수 있습니다. 이 연습에서는 이 기능을 검토하고 분석가의 경보 피로를 줄이기 위해 중간 및 높은 심각도의 클라우드용 Defender 경보만 Sentinel 인시던트로 승격하도록 필터링 옵션이 활성화된 규칙 하나를 만들어 보겠습니다.

> ✅ Note
> 모듈 1에서는 클라우드용 Microsoft Defender 보안 경고를 Sentinel과 동기화하는 클라우드용 Microsoft Defender 커넥터를 추가했습니다. 아래 설명된 대로 규칙을 만드는 것은 커넥터를 사용하지 않고도 수행할 수 있지만, 커넥터가 없으면 알림이 수집되거나 승격되지 않습니다.

1. Sentinel > Configuration > Analytics > 상단에 +Create > Microsoft incident creation rule 클릭합니다. 

   <img src="https://github.com/user-attachments/assets/e396bc25-d125-4d63-9967-4c7c2d4b4a3a" width="600"/>

2. General 설정합니다.
   * name: test -  MDC  Medium and High Alerts
   * Analytics rule logic
     * Microsoft security service: MDC 선택
     * Severity Custom하여 Mid/High로 선택 

   <img src="![image](https://github.com/user-attachments/assets/9926e1b0-eb97-4d7a-80f8-a4fb04bb8a98)
" width="600"/>

3. Automated response를 설정합니다.

> ✅ Automation rules란?
Sentinel의 **`Automation rules`**는 **인시던트가 생성되었을 때 조건에 따라 자동으로 후속 조치를 수행하는 규칙**입니다.

  * Automation rule name: AutoTag - High Risk Unpatched VMs
  * Trigger: When incident is created
  * Conditions: Severity > equals >  High
  * Action: Add tag - HighRiskVM

   <img src="https://github.com/user-attachments/assets/da5b7123-f12b-4750-a8b8-246200fcfe1b" width="600"/>

4. **Review and create**를 통해 Save 합니다.

      <img src="https://github.com/user-attachments/assets/749620ed-0d4e-43d4-83b6-c2ed41198a0a" width="600"/>

