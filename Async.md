# 비동기 통신 개발

```
DEVICE     PC        HOST
+---+      +---+     +---+
|   |      |   |     |   |
|   |      |   |     |   |
|   |      |   |     |   |
|   |      |   |     |   |
+---+      +---+     +---+









```

# Scenario



getData :
OID 만추출
우리서버의 id 를 추출
일치하지 않는것만 추출

cancel 요청

시간




# Test Data

# Test Code

``` java script
// const {log, clear} = console;
const FxJS = require ("fxjs")
// const _= require("fxjs/Strict")
const L = require("fxjs/Lazy")
const C = require("fxjs/Concurrency")

const {curry,takeWhile,flat,go,filter,take,log,reduce, map, tallAllC} = FxJS;

const rangeL = require("fxjs/Lazy/rangeL");

const delay = function (time,a) {
    return new Promise((resolve,reject) => {
        // resolve(a);
        setTimeout(() => resolve(a)
        ,time);         
    })
        
}

//(1) PLC 에게 Packet 보내서 받은 응답 (비동기)
//(2) 2배 해서
//(3) REST API 로 전송 (비동기)
// 1초 간격으로 무한 반복 

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
        return delay(1000*1,[{id:1},{id:0}]) //,{id:3},{id:7}
    },
    sendData: d => {
        console.log(`Rest API call for send Data to Server : ${d}`)
        return delay(1000*1,d)
    }
}



async function job() {
    const seqs = await go(L.range(1),
        HOST.getConfigs,
        L.map(c => c.id),
        take(Infinity),
    )
    log('SEQUENCE NO. : ',seqs)

    const PLCDatas = await go(
        seqs,
        L.map(DEVICE.getData),
        // takeWhile(seqs => seqs.length),
        flat,
        L.map(p => p.oid),
        take(Infinity),
    )   
  
    log('PLC Data : ',PLCDatas)
    
    const results = await go(
        PLCDatas,
        L.map(p => p*10),
        take(Infinity)
        )
    
    log('after Biz Logic (*10) : ',results)

    return await go (results,
         L.map(HOST.sendData),
         take(Infinity),
    )
    



}
async function recur() {
    return Promise.all([
        delay(1000*5),
        job()
    ]).then(recur)
}

recur();

```


