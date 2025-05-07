# Module 05. Playbook

## Sentinel Playbook? 
Microsoft Sentinel에서 Playbook이란, 보안 인시던트 발생 시 자동으로 실행되는 대응 절차를 정의한 **Logic App 기반 자동화 워크플로우**입니다.
* 쉽게 말해:
❗인시던트 발생 → 🔁 자동 조치(예: 이메일 알림, 티켓 생성, 사용자 격리 등)

*  개념 요약
  
| 항목        | 설명                                           |
| --------- | -------------------------------------------- |
| **정의**    | 인시던트 발생 시 자동 실행되는 Logic App 기반 워크플로우         |
| **플랫폼**   | Microsoft Logic Apps (Azure 기반 서버리스 자동화 플랫폼) |
| **트리거**   | 인시던트 생성, 엔티티 감지, 수동 실행 등                     |
| **활용 목적** | 보안 대응 자동화: 알림, 조치, 연동 등                      |

* Playbook으로 할 수 있는 일
  
| 시나리오                      | 예시                                       |
| ------------------------- | ---------------------------------------- |
| 📧 알림 발송                  | 보안 인시던트 발생 시 팀 메일/SMS로 알림                |
| 🛑 사용자 격리                 | 위험 사용자 계정을 Azure AD에서 즉시 차단              |
| 📥 티켓 생성                  | ServiceNow, Jira 등에 자동 티켓 생성             |
| 💬 Teams 메시지              | 특정 채널에 인시던트 정보 전송                        |
| 🔗 Threat Intelligence 연동 | VirusTotal, AbuseIPDB API로 IP 조회 후 자동 응답 |

* Sentinel Playbook 동작 구조
```markdown
Analytics Rule or Automation Rule
          ↓
     Sentinel Playbook
   (Azure Logic App 기반)
          ↓
   조건 분기 → 알림, 차단, 외부 연동


