---
layout: post
title: "[Node] Node + Oracle 연결하기"
date: '2019-10-06 16:00:00'
author: Heeye
categories: Node
tags: Node Oracle Docker
---

## Node + Oracle DB 연결하기
새로 옮긴 두번째 회사에서 Node + Oracle 연결하는 부분이 필요해 겸사겸사 기록💙

## Docker로 Oracle xe 11g 실행하기

docker official image를 사용하고 싶었으나 enterprise edition만 지원해서 jaspeen repo에 있는 이미지를 땡겨서 썼다. 실행은 아주 잘 된다.

```shell
docker login

docker pull jaspeen/oracle-xe-11g

docker run -d -p 8080:8080 -p 1521:1521 jaspeen/oracle-xe-11g

# system/oracle로 로그인
docker exec -it <<dockerPID>> sqlplus
```

## Node.js에서 Oracle DB 연결 예제

```shell
npm i oracledb

> oracledb@4.0.1 install /Users/heeye/dev/study/test-node/node_modules/oracledb
> node package/install.js

oracledb ********************************************************************************
oracledb ** Node-oracledb 4.0.1 installed for Node.js 11.6.0 (darwin, x64)
oracledb **
oracledb ** To use node-oracledb:
oracledb ** - Oracle Instant Client Basic or Basic Light package libraries must be in ~/lib or /usr/local/lib
oracledb **   Download from https://www.oracle.com/technetwork/topics/intel-macsoft-096467.html
oracledb **
oracledb ** Installation instructions: https://oracle.github.io/node-oracledb/INSTALL.html#instosx
oracledb ********************************************************************************
```

oracledb를 설치하면 Oracle Instant Client Basic이든 Basic Light든 깔아야 node-oracledb를 사용할 수 있다는 문구가 뜬다.

위에 안내처럼 OS에 맞는 oracle client를 https://www.oracle.com/technetwork/topics/intel-macsoft-096467.html 여기서 다운로드하고, 이후 설정방법은 https://oracle.github.io/node-oracledb/INSTALL.html#instosx 에 자세히 나와있다.

일단 나는 11g xe 기준이기 때문에

instantclient-basic-macos.x64-11.2.0.4.0.zip를 설치했다.

```shell

sudo mkdir -p /opt/oracle
cd /opt/oracle
unzip instantclient-basic-macos.x64-11.2.0.4.0.zip #in opt/oracle

#그리고나서 버전따라 가이드대로 하면 되는데, 난 12c 그 이하 버전이어서 아래와 같이 따랐다.
cp /opt/oracle/instantclient_11_2/{libclntsh.dylib.11.1,libnnz11.dylib,libociei.dylib} ~/lib/

sudo mkdir -p /opt/oracle/instantclient_12_2/network/admin
```

이렇게 node-oracledb와 oracle client 설치하고 설정을 끝낸 후 잘 동작하는지 확인은 아래 스크립트로 했다.


dbconfig.js
```js
module.exports = {
  user          : "system",
  password      : 'oracle',
  connectString : "0.0.0.0/xe"
};
```


connectOracle.js
```js

# 설정한 경로를 알맞게 채워넣기
var oracledb = require('./node_modules/oracledb');
var dbConfig = require('./dbconfig');

console.log('start to connect oracle-node!');

# 기본설정이 false라서 넣어준 옵션
oracledb.autoCommit = true;

oracledb.getConnection(
  {
    user          : dbConfig.user,
    password      : dbConfig.password,
    connectString : dbConfig.connectString
  },
  function(err, connection){

    if (err) {
      console.error(err.message);
      return;
    }

   var query = 'SELECT SEQ_NUM, USER_ID, USER_NAME, PROFILE FROM system.SHBEMP';

    connection.execute(query, function(err, result){
        if (err) {
          console.error(err.message);
          doRelease(connection);
          return;
        }

        console.log(result.metaData);
        console.log(result.rows);
        doRelease(connection);
      });
  });

function doRelease(connection) {
  connection.release(
    function(err) {
      if (err) {
        console.error(err.message);
      }
    });
}
```

이렇게 Node와 Oracle DB 연결은 끝!
