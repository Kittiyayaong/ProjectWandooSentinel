# Module 4. hunting

## 🔎 Sentinel의 "Hunting"이란?
룰에 안 걸리는 고급 공격을 직접 찾기 위한 위협 탐지 도구 세트
→ Sentinel에서는 이를 위해 Hunting Queries, Bookmarks, Live Stream, Entity Behavior 등을 제공합니다.

| Lab 번호    | 제목                                               | 목적                                | 주요 작업 요약                                                                                    | 연계 기능                        |
| --------- | ------------------------------------------------ | --------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------- |
| **Lab 1** | MITRE TTP 기반 헌팅<br>(T1098: Account Manipulation) | 특정 MITRE 기법에 해당하는 공격 탐지           | - Hunting > Queries 필터 `T1098`<br>- KQL 쿼리 실행<br>- Watchlist(`HighRiskApps.csv`) 생성 및 쿼리 조인 | Hunting Queries<br>Watchlist |
| **Lab 2** | Bookmark 지정                                      | 수상한 이벤트 수동 저장 (사후 조사, 포렌식 대비)     | - 쿼리 결과에서 특정 행 선택<br>- `Add bookmark` 클릭<br>- 이름, 태그(`Wandoo`) 설정 후 생성                      | Bookmark                     |
| **Lab 3** | Bookmark ➝ Incident 전환                           | 헌팅 결과를 인시던트(Incident)로 등록하여 대응 시작 | - Bookmark 상세 > `...` > `Create Incident`<br>- 제목, 심각도, 태그 설정<br>- Incident 저장 및 확인         | Incident Management          |

--

### Lab 1. 특정 MITRE 기법 찾기

시나리오: 공격 발생 후, 의심되는 TTP(예: T1098 Account Manipulation) 관련 모든 활동을 빠르게 확인하기 

1. Sentinel > Threat Management > Hunting > Queries로 이동하여 filter를 설정합니다. Filter은 Techniques > T1098로 지정합니다. 

   <img width="600" alt="image" src="https://github.com/user-attachments/assets/585cd671-db3c-4df2-95e3-41f688b1cb0a" />

> ⭐Tips. T1098? 
>
>  T1098은 Account Manipulation은 MITRE ATT&CK 프레임워크에 정의된 전술(Tactic) 중 하나인 **“Persistence (지속성 확보)”**에 해당하는 **기술(Technique)**로, 공격자가 침입 후, 계정을 조작해서 시스템에 계속 들어올 수 있게 만드는 수법

2. 수동 헌팅을 더 효율적으로 수행하기 위해, 나타난 모든 항목의 KQL 헌팅 쿼리 박스를 모두 클릭 후 상단에 **Run selected queries**를 클릭하여 진행합니다. 

   <img width="600" alt="image" src="https://github.com/user-attachments/assets/a5d86d7f-cd7f-48eb-918d-e4753816d626" />

4. Sentinel > Configuration > Watchlist로 이동 > 상단에 **New**를 클릭합니다.
   * name: HighRiskApps
   * Discription: High Risk Application
   * Allias: HighRiskApps
 
   <img width="600" alt="image" src="https://github.com/user-attachments/assets/0370cc1f-c106-45b5-acea-cb4846e0c016" />

5. [CSV 파일](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/WandooCSV/HighRIskApps.csv)을 다운로드 받습니다.

5. Watchlist 마법사페이지에서 4번에서 다운로드 받은 파일을 upload 한후, **SearchKey 항목에 AppName을 설정합니다. 이후 review + Create 하여 생성합니다. 

    <img src="https://github.com/user-attachments/assets/ca1c4663-4d96-47a7-b109-6d367d1adf8d" width="600"/>

6. 생성 후 watchlist에 반영되기까지 최대 5분정도 소요됩니다.

    <img src="https://github.com/user-attachments/assets/cdf10606-2c17-4089-9bfc-7c888463b4ac" width="600"/>

7. 쿼리를 클릭하여 우측 하단의 **View in logs**로 진입합니다.

    <img src="https://github.com/user-attachments/assets/407f43e0-0b66-4dda-90cb-af13809231e5" width="600"/>

8. 더 구체적인 조건의 쿼리로 기존 쿼리를 덮는 과정을 진행합니다. 기존 쿼리 교체를 통해 데이터를 보는 관점이 어떻게 바뀌는지, 쿼리를 정제가는 과정을 보실 수 있습니다. 신규쿼리는 아래와같은 정보를 포함합니다. 
   * CommandLine에 특정 키워드("Invoke")가 포함되어 있는지 필터
   * 사용자, 장치, 명령 실행 시점 등의 위협 인텔리전스 관점 반영
   * 실전 분석에 적합한 KQL 패턴 학습 목적
  
 9. User query를 클릭하여 query를 edit합니다.

    <img src="https://github.com/user-attachments/assets/905619cd-f594-4eae-b25f-64ad94f2035d" width="600"/>

> ⭐ 쿼리 목적
> 
> * High Risk로 지정된 애플리케이션(워치리스트 기반)에 누가, 어떤 IP에서, 언제, 어떤 키(Key)를 추가했는지 찾아냄
> * 해당 행위가 공격자 흔적일 수 있음 (ex: SPN key 추가)

 ```powershell
_GetWatchlist('HighRiskApps')
| join 
(
AuditLogs_CL
| where OperationName has_any ("Add service principal", "Certificates and secrets management")
| where Result_s =~ "success"
| mv-expand target = todynamic(TargetResources_s)
| where tostring(tostring(parse_json(tostring(parse_json(InitiatedBy_s).user)).userPrincipalName)) has "@" or tostring(parse_json(InitiatedBy_s).displayName) has "@"
| extend targetDisplayName = tostring(parse_json(TargetResources_s)[0].displayName)
| extend targetId = tostring(parse_json(TargetResources_s)[0].id)
| extend targetType = tostring(parse_json(TargetResources_s)[0].type)
| extend eventtemp = todynamic(TargetResources_s)
| extend keyEvents = eventtemp[0].modifiedProperties
| mv-expand keyEvents
| where keyEvents.displayName =~ "KeyDescription"
| extend set1 = parse_json(tostring(keyEvents.newValue))
| extend set2 = parse_json(tostring(keyEvents.oldValue))
| extend diff = set_difference(set1, set2)
| where isnotempty(diff)
| parse diff with * "KeyIdentifier=" keyIdentifier: string ",KeyType=" keyType: string ",KeyUsage=" keyUsage: string ",DisplayName=" keyDisplayName: string "]" *
| where keyUsage == "Verify" or keyUsage == ""
| extend AdditionalDetailsttemp = todynamic(AdditionalDetails_s)
| extend UserAgent = iff(todynamic(AdditionalDetailsttemp[0]).key == "User-Agent", tostring(AdditionalDetailsttemp[0].value), "")
| extend InitiatedByttemp = todynamic(InitiatedBy_s)
| extend InitiatingUserOrApp = iff(isnotempty(InitiatedByttemp.user.userPrincipalName), tostring(InitiatedByttemp.user.userPrincipalName), tostring(InitiatedByttemp.app.displayName))
| extend InitiatingIpAddress = iff(isnotempty(InitiatedByttemp.user.ipAddress), tostring(InitiatedByttemp.user.ipAddress), tostring(InitiatedByttemp.app.ipAddress))
| project-away diff, set1, set2, eventtemp, AdditionalDetailsttemp, InitiatedByttemp
| project-reorder
   TimeGenerated,
   OperationName,
   InitiatingUserOrApp,
   InitiatingIpAddress,
   UserAgent,
   targetDisplayName,
   targetId,
   targetType,
   keyDisplayName,
   keyType,
   keyUsage,
   keyIdentifier,
   CorrelationId
| extend
   timestamp = TimeGenerated,
   AccountCustomEntity = InitiatingUserOrApp,
   IPCustomEntity = InitiatingIpAddress
   ) on $left.AppName == $right.targetDisplayName
| where HighRisk == "Yes"
 ```
9. 상단에 새로운 query를 넣은 후, **Last 7 days**로 기준 변경하여 **Run**합니다.

   <img width="600" alt="image" src="https://github.com/user-attachments/assets/4a33e56c-547f-4ec9-b2ce-efb2af9018c8" />

---

### Lab 2. Bookmark 지정하기

> ✅ Bookmark란?
> 
> Bookmark는 수상한 보안 이벤트를 나중에 분석하거나 증거로 보존하기 위해 수동 저장하는 기능이며, 자동 인시던트 생성과는 독립적입니다.
→ 나중에 인시던트로 연결하거나 추가 분석을 위해 중요 데이터를 저장하는 메모 기능이라고 보면 됩니다.

| 항목        | 설명                                                          |
| --------- | ----------------------------------------------------------- |
| **정의**    | KQL 헌팅 결과에서 중요한 이벤트를 수동으로 저장                                |
| **목적**    | 수상한 이벤트를 추적하거나 나중에 인시던트로 전환                                 |
| **저장 위치** | `SecurityIncident`는 아님 → 별도의 Bookmark 저장소                   |
| **연계 가능** | Analytics Rule, Investigation Graph, Hunting 결과, Notebook 등 |

> ✅ 예시: 의심스러운 PowerShell 실행 로그 저장
DeviceProcessEvents
| where ProcessCommandLine has "Invoke-WebRequest"

* 결과 중 이상한 사용자/장치가 있다면 해당 행을 Bookmark로 저
* 이후 이 Bookmark를 기반으로 수동 인시던트를 만들 수 있음

1. 생성된 결과를 클릭하여, 상단에 **Add bookmark**를 클릭합니다.

    <img src="https://github.com/user-attachments/assets/146e3f32-37ee-4bf0-92eb-29815dd7ae68" width="600"/>

2. 우측에 bookmark 관련된 내용이 나오게 됩니다. detail한 정보를 기입합니다. 모든 정보 기입 후 하단에 **Create**를 클릭해서 완료합니다. 
   * Bookmark name은 victim@buildseccxpninja.onmicrosoft.com added key to purview-spn App with High Risk
   * Tag로 Wandoo를 넣습니다.

    <img src="https://github.com/user-attachments/assets/8f996fd7-8312-4c9c-aa12-02b37f40bf84" width="600"/>

---

### Lab 3. Bookmark를 promte하여 Incident로 등록하기

1. Hunting 페이지에서 **Bookmark**로 클릭하여, Lab 2번에서 생성한 bookmark를 클릭합니다. 우측 (...)을 클릭해서 **Create Incident**를 클릭합니다. 

 <img src="https://github.com/user-attachments/assets/db3f1c64-52ce-4db3-9dda-bbdea17c22b2" width="600"/>

2. 상세 정보 기입한 후 **Create**합니다. 
   * Title: victim@buildseccxpninja.onmicrosoft.com added key to purview-spn App with High Risk
   * Severity: Medium
   * Owner: 개인별로 설정합니다.
   * Tag: Wandoo

 <img src="https://github.com/user-attachments/assets/31a50095-45ef-4881-bbfc-8820bdbb21f6" width="600"/>

3. Incident 페이지로 넘어가서, 방금 생성한 incident를 확인합니다.

 <img src="https://github.com/user-attachments/assets/5776d9f1-ee51-4b94-8905-a0255a97ad0d" width="600"/>


### 🔗 [다음 Lab으로 이동하기 »](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/Module-04.Hunting.md)
