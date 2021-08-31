# 서버 개발자 
* 개발환경 : node.js, Visual Studio Code 
* 아래 Scenario 대로 동작하는 job() function 개발후 commit
* javascript / async, await, Promise 이해 필수 

## Scenario
 * (A) 에서 동작하는 Collector 의 시나리오 입니다ㅏ.
 *  각각의 함수는 비동기적으로 작동 한다. 
 *  각함수의 실행 순서는 유지 한다. 
 *  5초 간격으로 무한 반복 한다.

```
                       DEVICE         PC             SERVER
                       +---+          +---+          +---+
                       |   |          |   |          |   |   
                       |   |          |   | -------> |   |   HOST.getConfigs
                       |   |          |   | <------- |   |
       DEVICE.getData  |   |<-------  |   |          |   |   
                       |   |------->  |(A)|          |   |
                       |   |          |   |          |   |
                       |   |          |   | -------> |   |   HOST.sendData
                       |   |          |   | <------- |   |
                       |   |          |   |          |   |
                       +---+          +---+          +---+
```

## Sample code 설명
위의 시나리오를 구현하기 위한 javascript 코드 입니다. </br>
비동기 함수들은 delay 를 호출하여 timeout 후 call back 되도록 하여 </br>
SERVER,DEVICE 없이 비동기 상황을 테스트 할 수 있음 

* job() 함수 구현후 제출

### Sample Code

``` java script
const log = console.log;

//비동기 상황을 만들기 위해 time 시간 지난후 a 반환
const delay = function (time,a) {
    return new Promise((resolve,reject) => {
        // resolve(a);
        setTimeout(() => resolve(a)
        ,time);         
    })
        
}

const DEVICE = {  
    packetMap: {  // Device Map => [seq no : Device Data]
        //seq : 
        0: [{iid:11,oid:1},{iid:12,oid:2},{iid:13,oid:3}],
        1: [{iid:14,oid:4},{iid:15,oid:5},{iid:16,oid:6}],
        2: [{iid:17,oid:7},{iid:18,oid:8}],
        3: [],
        4: []

    },
    getData: seq => { // input : seq, 출력: Device Data
        console.log(`Send seq no. to Device for get Data : SEQ=${seq}`);
        return delay(1000 * 1,DEVICE.packetMap[seq]);
    }
}

const HOST = {
    getConfigs: p => {
        return delay(1000*1,[{id:1},{id:0}])
    },
    sendData: d => {
        console.log(`Rest API call for send Data to Server : ${d}`)
        return delay(1000*1,d)
    }
}



async function job() {
    // config 얻기 위해 호출
    // 비동기 대기후
    // 얻어진 {id:1},{id:0}
    // 에서 DEVICE 에게 요청할 seq 만 추출 [1,0]  // P => P.id
    // (1)
    HOST.getConfigs
    log('SEQUENCE NO. : ',seqs)

    // getData(seq) 호출하여
    // seq 에 매핑된 데이터 수신후
    // REST API 전달할 oid 만 추출
    // (2)
    DEVICE.getData,
    log('Device Data : ',DeviceDatas)

    // 가상 biz Logic : 
    // DEVICE 에서 받은 Data * 10 // p => p*10
    // (3)
    const results = PLC 에서 받은 데이터 * 10
    
    log('after Biz Logic (*10) : ',results)

    // REST API 호출
    // (4)
     HOST.sendData

}

// job 을 매 5초 간격으로 실행
// job 이 5초 이상 걸리면
// job 이 끝날때까지 대기후 job 수행
// job 이 5초 이내 완료 되면
// 5초간 대기후 job 수행
async function recur() {
    return Promise.all([
        delay(1000*5),
        job()
    ]).then(recur)
}

recur();




```
### Sample Code 결과
위의 코드를 실행하면 아래와 같이 비동기 함수들이 순차적으로 수행된다. </br>
* 결과로그 
```
SEQUENCE NO. :  [ 1, 0 ]
Send seq no. to Device for get Data : SEQ=1
Send seq no. to Device for get Data : SEQ=0
Device Data :  [ 4, 5, 6, 1, 2, 3 ]
after Biz Logic (*10) :  [ 40, 50, 60, 10, 20, 30 ]
Rest API call for send Data to Server : 40
Rest API call for send Data to Server : 50
Rest API call for send Data to Server : 60
Rest API call for send Data to Server : 10
Rest API call for send Data to Server : 20
Rest API call for send Data to Server : 30
SEQUENCE NO. :  [ 1, 0 ]
Send seq no. to Device for get Data : SEQ=1
Send seq no. to Device for get Data : SEQ=0
Device Data :  [ 4, 5, 6, 1, 2, 3 ]
after Biz Logic (*10) :  [ 40, 50, 60, 10, 20, 30 ]
Rest API call for send Data to Server : 40
Rest API call for send Data to Server : 50
Rest API call for send Data to Server : 60
Rest API call for send Data to Server : 10
Rest API call for send Data to Server : 20
Rest API call for send Data to Server : 30
SEQUENCE NO. :  [ 1, 0 ]
Send seq no. to Device for get Data : SEQ=1
Send seq no. to Device for get Data : SEQ=0
Device Data :  [ 4, 5, 6, 1, 2, 3 ]
after Biz Logic (*10) :  [ 40, 50, 60, 10, 20, 30 ]
Rest API call for send Data to Server : 40
Rest API call for send Data to Server : 50
Rest API call for send Data to Server : 60
Rest API call for send Data to Server : 10
Rest API call for send Data to Server : 20
Rest API call for send Data to Server : 30

```

# Client 개발자 
* 개발환경 : vue.js, vuetify, Visual Studio Code 
* 위 서버의 의 Message 가 {x : [ 40, 50, 60, 10, 20, 30 ]} 로 전달될경우
* 실시간으로 x+1 하는 UI 작성 / compose 기능 사용
* javascript / vue.js 이해 필수 


# momngoDB 개발자 
* 개발환경 : mongoDBCampas.js
* 위 서버의 의 Message 가 {x : [ 40, 50, 60, 10, 20, 30 ]} 로 전달될경우
* Collection X 에 실시간으로 저장, ChangeStream Event 발생
* mongoDB 이해 필수 
