# UI
Visual Studio Code, Vue, Vuetify, Javascript,   

* 화면 개발자 요구스펙. 
  * Vue Component 로 작성
  * 실시간 데이터 변경
* 개발환경
* 제출 
  *    

# 서버
Visual Studio Code, nodejs, express, logger, RestAPI, swagger, MongoDB(ChangeStrram)

* 서버 개발자 요구 스펙
  * CRUD (Status, History)
  * Authentication / Authorization 
  * logging
  * Exception Handling
  * 비동기 통신(예제 참조)
* 테스트
예제의 mongoDB 에 접속하면 가상의 설비데이터를 수신 할 수 있습니다. 
  * 설비데이터 수신 기능 개발
    * mongoDB 접속 주소 mongodb+srv://janghoon:<password>@cluster0.ocpuy.mongodb.net/test
      * DB: EQData_Test , collection : EQStatus(현황) ,H_EQStatus(이력)
    * sample Data 수신
    * CRUD message  
  * 예제의 mongoDB로 가상의 설비 데이터를 송신 기능 개발.

* 제출
  * CRUD 를 위한 RestAPI 를 작성 하세요(10)  
  * 가상 데이터 발생기로 데이터를 송신 하세요 (update Rest API 호출)
    * 파일 watch - Chokidar (10)
    * LS 산전 PLC 프로토콜 or ModeBus 프로토콜 (10)
  * changestream 데이터를 수신하여 상태를 기록하고 history 에 append 하세요 (20)
    * https://www.mongodb.com/developer/quickstart/nodejs-change-streams-triggers/
    * https://github.com/mongodb-developer/nodejs-quickstart
  * 서버에서 발생하는 log 를 기록 하세요 (10)
    * [winston logger](https://www.datadoghq.com/blog/node-logging-best-practices/#create-a-custom-logger-for-your-application)
    * [winston](https://www.youtube.com/watch?v=S0HI5DDCBBs)
    * [winston+mongodb](https://www.youtube.com/watch?v=PdVlAi7nrRU) / [winston+mongodb+git](https://github.com/Tariqu/winston_logger_example)
  * Exception 처리를 하세요 (10)
    * https://www.youtube.com/watch?v=GAJhNielqqA
    * https://www.youtube.com/watch?v=DyqVqaf1KnA
 
# DevOPs
Jenkins, SVN, Ansible, Powershell
* 요구 사항
  * CI / CD 
  * Ansible Setup
  * PUSH/PULL deploy 
* 제출
  * 환경 구축 (10)
    * jenkins 설치
    * ansible 설치
    * powershell 연결
  * Push (5)
  * Pull (5)
