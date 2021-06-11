# EQDataConnector
MCNex 프로젝트 산출물과 신규 개발 솔루션 suit 이름 입니다.  

* 아래 기능을 Enterprise Level 로 수행 합니다. 
  * 기능 (1) Enterprise Level 의 수집 , 저장, 공유, 로직 수행
  * 기능 (2) Enterprise Level 의 명령, 수행, 결과 보고
  * 기능 (3) 수집데이터 표준화
  * 기능 (4) Virtual EQ, Admin 을 활용한 개발 효율성, 운영 효율성 향상
  * 기능 (5) CI/CD 를 활용한 개발한 개발 생산성  향상

* 고가용성을 확보 하기 위해 시스템간 이중화를 합니다
  * Reverse Proxy 이중화
  * NoSQLDB replicaSet 구성
  * Biz logic 자동 Restart
  * 수집기 장애 발생시 Data Buffering

## 구성
* EQDataCollector, EQDataHub, NoSQLDB(MongoDB/ChangeStream),EQDataViewer
* ReverseProxxy,EQDataVirtualEQ
### EQData Collector
설비에서 Data 를 수집 하여 표준 Data 포멧으로 변경하여 전송 합니다. 
### EQData HUB
* 설비에서 Data 를 수신 위하여 REST API 를 제공하여 DB 에 저장 합니다. 
* 다른 시스템에서 설비 Data 요청시 저장된 Data 를 제공 합니다. 

### MongoDB
* SQLDB 로 Json 객제를 저장 합니다. 
*  마지막 값을 Status 에 실시간 업데이트 합니다. 
* atus 값이 변경되면 이력을 자동으로 생성 합니다. 
* 설정값을 관리 합니다. 

### EQDataViewer
* 설비에서 올라온 Data 를 실시간으로 볼수 있는 Biz logic Application 입니다. 

### Reverse Proxy (Nginx)
* 설비는 단일 IP 로 데이터를 전송 합니다. 
* Reverse Proxy 에서 내부 서버로 Load Balancing 합니다. 

### EQDataVirtualEQ
* 설비에서 생성 되는 FILE 을 Emulate 하여 가상의 Data 를 만듭니다.  


