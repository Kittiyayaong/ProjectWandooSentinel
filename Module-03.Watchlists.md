# Module 3. Watchlists

## Watchlist
Sentinel 내에서 특정 데이터(예: IP, 사용자, 해시 등)를 참조 리스트로 저장하여 분석 규칙, 쿼리, 헌팅 등에서 활용할 수 있도록 지원하는 기능입니다.

* 활용 예시
  * 내부 테스트/침투 테스트 IP를 사전에 등록하여 탐지 제외 처리
  * 특정 VIP 사용자, 관리자 계정 등을 우선 감시 또는 예외 처리 가능
  * 향후 Logic App 또는 Workbook 시각화에서 Watchlist 연동 가능

---

## Lab 1: Watchlist 생성하기

### 목적
* 침투 테스트나 차단 대상 IP 목록 등을 **CSV로 등록**하여 Sentinel 내에서 탐지 제외 또는 강조 대상으로 활용

### 실습 단계

1. Sentinel > Configuration > Watchlist > 상단에 **New**를 클릭합니다.

  <img src="https://github.com/user-attachments/assets/ca57c4ea-8025-4475-855b-74af09d69afe" width="600">

2. 정보를 기입합니다.
     * Name: TestsIPaddresses
     * Description: IP addresses used during penetration tests
     * Watchlist Alias: TestsIPaddresses

3. [CSV 파일](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/WandooCSV/TestIPaddress.csv)을 다운로드 받아서, 업로드 합니다. Searchkey는 **IPAddress**로 설정하여 create합니다.

    <img src="https://github.com/user-attachments/assets/5fdb122d-9adc-4ae0-be04-7c2d74f7cc35" width="600">

4. 생성 이후 5분 후에 Watchlist에 올라오게됩니다.

---

## Lab 2: Watchlist를 활용한 Analytics Rule 설정 (분석 규칙에 IP 주소 화이트리스트 추가하기 (Watchlist를 활용한 실전 탐지 자동화(Analytics Rule)로 연결하기)

### 목적
* 기존에 설치된 Analytics Rule에 Watchlist 기반 필터 추가
  * 예: 침투 테스트용 IP는 제외 또는 필터링 처리

### 실습 단계

1. Contents Hub에서 **High count of connection**을 클릭하여 install합니다.

> ⭐Tips. High count of connection
> 
> Microsoft Sentinel의 분석 규칙 템플릿(Analytics Rule Template) 중 하나로,
"특정 IP 또는 클라이언트가 단시간에 과도하게 많은 연결을 시도하는" 이상행위를 탐지하기 위한 룰입니다.

  <img src="https://github.com/user-attachments/assets/9ca3a4e1-7397-4161-9758-3ee587a860f7" width="600">

2. install이 완료된 후, **Create rue**을 클릭합니다.

  <img src="https://github.com/user-attachments/assets/0756ce76-c861-4995-afb5-90518fd375cd" width="600">

3. 다음 KQL 문을 추가해서 "TestIPaddress" Watchlist에서 필드를 가져옵니다. (let문 가장 하단에 추가하시면 됩니다.)
 
 ```powershell
let TestIPaddress= _GetWatchlist('TestIPaddress') | project SearchKey ;
W3CIISLog
| where cIP in (TestIPaddress)
 ```
> ⭐Tips. KQL문 내용
>
> Microsoft Sentinel의 Watchlist에 등록된 IP 목록을 불러와, W3CIISLog (웹 서버 접속 로그)에서 해당 목록에 포함된 IP만 필터링하는 쿼리입니다.

4. 이제 클라이언트 IP 주소(cIP 필드)가 감시 목록의 IP 주소 중 하나와 일치하는 레코드를 삭제하기위해 where 문을 추가합니다.
 ```powershell 
| where cIP in (TestIPaddress)
 ```
   <img src="https://github.com/user-attachments/assets/9a95ada0-4352-4096-92fe-24831ceead13" width="600">

* 전문
 ```powershell
let timeBin = 10m;
let portThreshold = 30;
let TestIPaddress= _GetWatchlist('TestIPaddress') | project SearchKey ;
W3CIISLog
| where cIP in (TestIPaddress)
| extend scStatusFull = strcat(scStatus, ".",scSubStatus)
// Map common IIS codes
| extend scStatusFull_Friendly = case(
scStatusFull == "401.0", "Access denied.",
scStatusFull == "401.1", "Logon failed.",
scStatusFull == "401.2", "Logon failed due to server configuration.",
scStatusFull == "401.3", "Unauthorized due to ACL on resource.",
scStatusFull == "401.4", "Authorization failed by filter.",
scStatusFull == "401.5", "Authorization failed by ISAPI/CGI application.",
scStatusFull == "403.0", "Forbidden.",
scStatusFull == "403.4", "SSL required.",
"See - https://support.microsoft.com/help/943891/the-http-status-code-in-iis-7-0-iis-7-5-and-iis-8-0")
// Mapping to Hex so can be mapped using website in comments above
| extend scWin32Status_Hex = tohex(tolong(scWin32Status))
// Map common win32 codes
| extend scWin32Status_Friendly = case(
scWin32Status_Hex =~ "775", "The referenced account is currently locked out and cannot be logged on to.",
scWin32Status_Hex =~ "52e", "Logon failure: Unknown user name or bad password.",
scWin32Status_Hex =~ "532", "Logon failure: The specified account password has expired.",
scWin32Status_Hex =~ "533", "Logon failure: Account currently disabled.",
scWin32Status_Hex =~ "2ee2", "The request has timed out.",
scWin32Status_Hex =~ "0", "The operation completed successfully.",
scWin32Status_Hex =~ "1", "Incorrect function.",
scWin32Status_Hex =~ "2", "The system cannot find the file specified.",
scWin32Status_Hex =~ "3", "The system cannot find the path specified.",
scWin32Status_Hex =~ "4", "The system cannot open the file.",
scWin32Status_Hex =~ "5", "Access is denied.",
scWin32Status_Hex =~ "8009030e", "SEC_E_NO_CREDENTIALS",
scWin32Status_Hex =~ "8009030C", "SEC_E_LOGON_DENIED",
"See - https://msdn.microsoft.com/library/cc231199.aspx")
// decode URI when available
| extend decodedUriQuery = url_decode(csUriQuery)
// Count of attempts by client IP on many ports
| summarize makeset(sPort), makeset(decodedUriQuery), makeset(csUserName), makeset(sSiteName), makeset(sPort), makeset(csUserAgent), makeset(csMethod), makeset(csUriQuery), makeset(scStatusFull), makeset(scStatusFull_Friendly), makeset(scWin32Status_Hex), makeset(scWin32Status_Friendly), ConnectionsCount = count() by bin(TimeGenerated, timeBin), cIP, Computer, sIP
| extend portCount = arraylength(set_sPort)
| where portCount >= portThreshold
| where cIP in (TestIPaddress)
| project TimeGenerated, cIP, set_sPort, set_csUserName, set_decodedUriQuery, Computer, set_sSiteName, sIP, set_csUserAgent, set_csMethod, set_scStatusFull, set_scStatusFull_Friendly, set_scWin32Status_Hex, set_scWin32Status_Friendly, ConnectionsCount, portCount
| order by portCount
 ```

5. Save하여 완료합니다.


### 🔗 [다음 Lab으로 이동하기 »](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/Module-04.Hunting.md)


