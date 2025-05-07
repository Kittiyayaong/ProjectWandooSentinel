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

### Lab 1. Playbook 만들기

1. Sentinel > Automation에서 신규 **Blank playbook**를 생성합니다. 

  <img src="https://github.com/user-attachments/assets/9f7be2eb-12a9-4e81-97eb-784f0e97505d" width="1000">

--- 

> ✅ Tips. Azure Logic App을 생성할 때 선택하는 **호스팅 플랜(Hosting Plan)**

 <img src="https://github.com/user-attachments/assets/e9245b45-c757-4dae-87d9-0336a1b3f754" width="1000">

| 구분                                        | 설명                                                                                           |
| ----------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Consumption > Multi-tenant**            | <br>✅ 가장 단순하고 저렴한 방식<br>✅ 공유 인프라에서 실행됨<br>✅ 호출당 과금(Pay-per-operation)<br>❌ 전용 리소스/VNET 통합 불가 |
| **Standard > Workflow Service Plan**      | <br>✅ 단일 테넌트 환경 (보안/성능 우수)<br>✅ VNET 통합 가능<br>✅ 특정 워크플로 인스턴스 기준 과금<br>❌ 초기 설정 필요             |
| **Standard > App Service Environment v3** | <br>✅ 대규모 엔터프라이즈 전용<br>✅ 완전한 격리 및 확장성<br>❌ 비용 매우 높음, 관리 복잡                                   |
| **Hybrid (PREVIEW)**                      | <br>✅ 로컬 또는 Kubernetes 기반에서 실행 가능<br>✅ 멀티클라우드/온프레미스에 유리<br>❌ 아직 Preview 단계, 실무 적용에 주의 필요     |

* 항목별 기능 비교

| 항목          | Multi-tenant | Workflow Plan | ASE v3        | Hybrid               |
| ----------- | ------------ | ------------- | ------------- | -------------------- |
| **컴퓨팅 리소스** | 공유됨 (Shared) | 전용(Dedicated) | 전용(Dedicated) | 고객이 직접 관리            |
| **네트워크 통합** | 불가           | VNET 통합 가능    | VNET 통합 가능    | 로컬 네트워크 직접 접근        |
| **요금 체계**   | 호출당 과금       | 인스턴스 기준       | ASE 단위 과금     | vCPU 기준 (Kubernetes) |
| **보안/격리**   | 낮음           | 중간\~높음        | 매우 높음         | 상황에 따라 다름            |

* 언제 무엇을 선택할까?

| 상황                                | 추천 플랜                         |
| --------------------------------- | ----------------------------- |
| 단순, 가벼운 자동화 / 테스트                 | ✅ Multi-tenant (Consumption)  |
| Sentinel Playbook 운영 / VNET 통합 필요 | ✅ Workflow Service Plan       |
| 보안 민감한 대규모 기업 환경                  | 🔒 App Service Environment v3 |
| 쿠버네티스 기반 멀티클라우드 운영                | 🌐 Hybrid (Preview 상태 주의)     |

---

2. **Consumption**을 클릭합니다.
   
 <img src="https://github.com/user-attachments/assets/577d3c42-9e4f-4b01-9650-caf3e9a1bcc8" width="1000">

3. 정보를 기입한 후, Create하여 완료합니다. 

 <img src="https://github.com/user-attachments/assets/ff50cc64-e59b-4e33-94b3-911287a0ebaf" width="600">
