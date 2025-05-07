# Module 4. hunting

## 🔎 Sentinel의 "Hunting"이란?
룰에 안 걸리는 고급 공격을 직접 찾기 위한 위협 탐지 도구 세트
→ Sentinel에서는 이를 위해 Hunting Queries, Bookmarks, Live Stream, Entity Behavior 등을 제공합니다.

* 사용 예시

| 상황                  | Hunting 사용 여부                            |
| ------------------- | ---------------------------------------- |
| 사후 분석/포렌식           | ✅ 적극 권장                                  |
| 침해 우려 상황 조사         | ✅ 예                                      |
| 룰 기반 탐지로는 놓치는 위협 찾기 | ✅ 예                                      |
| 실시간 이상 징후 대응        | ❌ (Analytics Rules나 Automation 사용이 더 적합) |

* 구성 요소 

| 구성 요소                      | 설명                                                  |
| -------------------------- | --------------------------------------------------- |
| **Hunting Queries**        | 미리 정의된 KQL 기반 위협 탐지 쿼리 (Microsoft에서 제공하거나 직접 생성 가능) |
| **Bookmarks**              | 조사 중 저장한 중요 이벤트 스냅샷. 추후 사건 조사나 Incidents 생성에 사용     |
| **Livestream**             | 특정 KQL 쿼리를 실시간으로 감시 (지속적으로 새 데이터 모니터링)              |
| **Notebooks**              | Jupyter 기반 자동 분석/시각화 도구. Threat Hunting 워크플로우를 자동화  |
| **Entity Behavior (UEBA)** | 사용자/호스트의 이상 행동 분석과 연계하여 사후 추적 가능                    |


### Lab 1. 특정 MITRE 기법 찾기

시나리오: 공격 발생 후, 의심되는 TTP(예: T1098 Account Manipulation) 관련 모든 활동을 빠르게 확인하기 

1. Sentinel > Threat Management > Hunting > Queries로 이동하여 filter를 설정합니다. Filter은 Techniques > T1098로 지정합니다. 
   
  <img src="![image](https://github.com/user-attachments/assets/177e200e-59f4-421c-8227-971ca3cba0b8)" width="600"/>

2. 수동 헌팅을 더 효율적으로 수행하기 위해, 나타난 모든 항목의 KQL 헌팅 쿼리 박스를 모두 클릭 후 상단에 **Run selected queries**를 클릭하여 진행합니다. 

 <img src="https://github.com/user-attachments/assets/12eef7fd-00b1-43c3-a50e-4d7a075eb233" width="600"/>

3. Sentinel > Configuration > Watchlist로 이동 > 상단에 **New**를 클릭합니다.

 <img src="https://github.com/user-attachments/assets/3e243902-1636-4028-b6df-78961c3e8c36" width="600"/>

> ✅ Watchlist란?
> Microsoft Sentinel의 **Watchlist**는 보안 운영팀이 주기적으로 참조해야 할 중요한 사용자, IP, 장치, 도메인, 해시값 등의 목록을 저장하고 분석 규칙에서 쉽게 참조할 수 있도록 도와주는 기능입니다.

> ✅ 주요 활용 시나리오
>   * 감시 대상 사용자/그룹 설정 (VIP, 퇴사 예정자 등)
>   * 차단된 IP 목록 비교
>   * 의심 파일 해시 목록
>   * 테넌트 간 보안 정보 공유

4. [CSV 파일](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/WandooCSV/HighRIskApps.csv)을 다운로드 받습니다.

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

 <img src="https://github.com/user-attachments/assets/14901c63-dcae-4dfa-8b29-5a776f332dfd" width="600"/>

### Lab 2. Bookmark 지정하기

> ✅ Bookmark란?
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
