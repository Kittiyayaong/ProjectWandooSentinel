# Module 3. Watchlists

### Lab 1. Watchlist 생성하기 

1. Sentinel > Configuration > Watchlist > 상단에 **New**를 클릭합니다.

  <img src="https://github.com/user-attachments/assets/ca57c4ea-8025-4475-855b-74af09d69afe" width="600">

2. 정보를 기입합니다.
     * Name: PenTestsIPaddresses
     * Description: IP addresses used during penetration tests
     * Watchlist Alias: PenTestIPaddresses

    <img src="https://github.com/user-attachments/assets/8c73ce24-088f-41eb-9296-ecfbef61f826" width="600">

3. [CSV 파일](https://github.com/Kittiyayaong/ProjectWandooSentinel/blob/main/WandooCSV/TestIPaddress.csv)을 다운로드 받아서, 업로드 합니다. Searchkey는 **IPAddress**로 설정하여 create합니다.

    <img src="https://github.com/user-attachments/assets/5fdb122d-9adc-4ae0-be04-7c2d74f7cc35" width="600">

4. 생성 이후 5분 후에 Watchlist에 올라오게됩니다.


### Lab 2. 분석 규칙에 IP 주소 화이트리스트 추가하기 (Watchlist를 활용한 실전 탐지 자동화(Analytics Rule)로 연결하기)

1. Contents Hub에서 **High count of connection**을 클릭하여 install합니다.

  <img src="https://github.com/user-attachments/assets/9ca3a4e1-7397-4161-9758-3ee587a860f7" width="600">

2. install이 완료된 후, **Create rue**을 클릭합니다.

  <img src="https://github.com/user-attachments/assets/0756ce76-c861-4995-afb5-90518fd375cd" width="600">

3. 다음 KQL 문을 추가해서 "TestIPaddress" Watchlist에서 필드를 가져옵니다.
 
 ```powershell
let TestIPaddress= _GetWatchlist('TestIPaddress') | project SearchKey ;
W3CIISLog
| where cIP in (TestIPaddress)
 ```

![image](https://github.com/user-attachments/assets/ed066ff0-5f77-4f03-a31b-ee5b9aa2cd3a)


4. 이제 클라이언트 IP 주소(cIP 필드)가 감시 목록의 IP 주소 중 하나와 일치하는 레코드를 삭제하기위해where 문을 추가합니다.
 ```powershell 
| where cIP in (TestIPaddress)
 ```
   <img src="https://github.com/user-attachments/assets/9a95ada0-4352-4096-92fe-24831ceead13" width="600">

5. Save하여 완료합니다.


