# Module 3. hunting

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

3. Watchlist
