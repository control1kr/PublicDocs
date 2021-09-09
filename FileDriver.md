# File Driver 개발자 
* 개발환경 : chokidar, node.js, Visual Studio Code 
* 아래 Scenario 대로 동작하는 job() function 개발후 commit
* javascript / async, await, Promise 이해 필수 

## Scenario
 * (A) 에서 동작하는 Collector 의 시나리오 입니다.

 * 초기화 :      

    * 지정된 파일 타입만 모니터링 한다. / filtering : specific file types 
    * 지정된 폴더에 오래된 파일이 있으면 삭제 한다. /  delete old files more than 30 days;
    * 네트워크 연결 해서 파일을 수집 해야 하면 네트워크 폴더에 접속 한다. / (if) network : logon(   id / pw )

 * 이동

    * 모니터링 중 패턴에 맞는 파일을 Work Folder 로 Move 한다. (작업대상 선별 ex) *.csv)

 * Processing

    * 파일을 파싱한다.
    * 파싱된 내용을 표준 json 타입으로 변환한다

 * 보고

    * 표준 json 을 Rest API 로 전송한다.
    * OK 받으면 Backup 폴더로 파일을 전송 한다. 

 * 후처리 (스케줄러)

    * Buckup 폴더에 날자별로 원본파일을 backup 한다.

    * 30일 지난 폴더/파일들은 삭제 한다. 

      

 *  Chokidar(FileWatcher) 에서 Event  감지 한다. 

```
                       FILE           PC             SERVER
                       +---+          +---+          +---+
                       |   |          |   |          |   |   
                       |   |          |   | -------> |   |   HOST.getConfigs
                       |   |          |   | <------- |   |
       Chokidar/ Event |   | -------> |   |          |   |   
                       |   |          |(A)|          |   |
                       |   |          |   |          |   |
                       |   |          |   | -------> |   |   HOST.sendData
                       |   |          |   | <------- |   |
                       |   |          |   |          |   |
                       +---+          +---+          +---+
```

## Sample code 설명
위의 시나리오를 구현 및 검증 하기 위한 test code 입니다. . </br>
예제 테스트 코드에 chokidar 를 사용하는 방법은 Sample code에 상세히 나와 있습니다.  </br>

### Sample Code (Chokidar test.js)

* [chokidar](https://www.npmjs.com/package/chokidar)
* [chokidar/testcode/test.js](https://github.com/paulmillr/chokidar/blob/master/test.js)



``` java script
/* eslint-env mocha */

'use strict';

const fs = require('fs');
const sysPath = require('path');
const {promisify} = require('util');
const childProcess = require('child_process');
const chai = require('chai');
const rimraf = require('rimraf');
const sinon = require('sinon');
const sinonChai = require('sinon-chai');
const upath = require('upath');

const {isWindows, isMacos, isIBMi, EV_ALL, EV_READY, EV_ADD, EV_CHANGE, EV_ADD_DIR, EV_UNLINK, EV_UNLINK_DIR, EV_RAW, EV_ERROR}
    = require('./lib/constants');
const chokidar = require('.');

const {expect} = chai;
chai.use(sinonChai);
chai.should();

const exec = promisify(childProcess.exec);
const write = promisify(fs.writeFile);
const fs_symlink = promisify(fs.symlink);
const fs_rename = promisify(fs.rename);
const fs_mkdir = promisify(fs.mkdir);
const fs_rmdir = promisify(fs.rmdir);
const fs_unlink = promisify(fs.unlink);
const pRimraf = promisify(rimraf.
const FIXTURES_PATH_REL = 'test-fixtures';
const FIXTURES_PATH = sysPath.join(__dirname, FIXTURES_PATH_REL);
const allWatchers = [];
const PERM_ARR = 0o755; // rwe, r+e, r+e
let subdirId = 0;
let options;
let currentDir;
let slowerDelay;

// spyOnReady
const aspy = (watcher, eventName, spy = null, noStat = false) => {
  if (typeof eventName !== 'string') {
    throw new TypeError('aspy: eventName must be a String');
  }
  if (spy == null) spy = sinon.spy();
  return new Promise((resolve, reject) => {
    const handler = noStat ?
      (eventName === EV_ALL ?
      (event, path) => spy(event, path) :
      (path) => spy(path)) :
      spy;
    watcher.on(EV_ERROR, reject);
    watcher.on(EV_READY, () => resolve(spy));
    watcher.on(eventName, handler);
  });
};

const waitForWatcher = (watcher) => {
  return new Promise((resolve, reject) => {
    watcher.on(EV_ERROR, reject);
    watcher.on(EV_READY, resolve);
  });
};

const delay = async (time) => {
  return new Promise((resolve) => {
    const timer = time || slowerDelay || 20;
    setTimeout(resolve, timer);
  });
};

const getFixturePath = (subPath) => {
  const subd = subdirId && subdirId.toString() || '';
  return sysPath.join(FIXTURES_PATH, subd, subPath);
};
const getGlobPath = (subPath) => {
  const subd = subdirId && subdirId.toString() || '';
  return upath.join(FIXTURES_PATH, subd, subPath);
};
currentDir = getFixturePath('');

const chokidar_watch = (path = currentDir, opts = options) => {
  const wt = chokidar.watch(path, opts);
  allWatchers.push(wt);
  return wt;
};

const waitFor = async (spies) => {
  if (spies.length === 0) throw new TypeError('SPies zero');
  return new Promise((resolve) => {
    const isSpyReady = (spy) => {
      if (Array.isArray(spy)) {
        return spy[0].callCount >= spy[1];
      }
      return spy.callCount >= 1;
    };
    let intrvl, timeo;
    function finish() {
      clearInterval(intrvl);
      clearTimeout(timeo);
      resolve();
    }
    intrvl = setInterval(() => {
      process.nextTick(() => {
        if (spies.every(isSpyReady)) finish();
      });
    }, 20);
    timeo = setTimeout(finish, 5000);
  });
};

const dateNow = () => Date.now().toString();

.
.
.
.



```
### File Driver Test 예상(초기)
* 위의 테스트 코드는 (mocha...) 등을 사용 했는데 단순화를 위해서 아래 테스트 코드는 jest 를 사용 했습니다. [jest](https://www.youtube.com/watch?v=g4MdUjxA-S4)
* File Driver 예상 테스트 시나리오
* 아래 시나리오에 맞춰서 본 코드를 작성 하세요
```javascript

describe('Local Computer',() => {
  describe('Success',()=> {
    describe('Detect and Move',()=> {
      test('set watching directory',()=>{})
      test("Detect folder : delete n days ago",()=>{})
      test('filtering : csv only  ignore except csv',()=>{})
      test('find and move',()=>{})
    
    
    })
  

  })

  describe('Fail',()=> {
    test('set watching directory : Throw : Can not find specfic directory : ',()=>{})
    test('argument : path is not correct',()=>{})
    
  })


})



describe('Network Computer',() => {
  describe('Success', () => {
    test('LogOn : sucess one time',()=> { })
    test('LogOn : success 10 times retry finally reconnect ',()=> { })
    
    test('LogOn : Wrong ip',()=> { })
    test('LogOn : Wrong id',()=> { })
    test('LogOn : Wrong pw',()=> { })

  })

  describe('Fail', () => {
    
  })


});
```



# Client 개발자 

* 개발환경 : vue.js, vuetify, Visual Studio Code 
* 위 서버의 의 Message 가 {x : [ 40, 50, 60, 10, 20, 30 ]} 로 전달될경우
* 실시간으로 x+1 하는 UI 작성 / compose 기능 사용
* javascript / vue.js 이해 필수 


# 요구조건 
* File Handling // 생성, 삭제, 이동, 
* File Parsing // csv, txt
* File Data Converting // Json
* Rest API 호출
