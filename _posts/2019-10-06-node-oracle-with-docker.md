---
layout: post
title: "[Node] Node + Oracle 연결이 가능한 Container Image 만들기"
date: '2019-10-06 18:00:00'
author: Heeye
categories: Node
tags: Node Oracle Docker
---

## Node + node-oracledb + oracle client = Custom Container

Container로 동작할 수 있는 node image가 있어야 하는데, oracle DB 연결이 가능해야 한다.
Node에서 Oracle DB 연결을 위해선 client 설치와 환경설정만 하면 간단하다.

**먼저 Dockerfile 작성**
```Dockerfile
#https://blogs.oracle.com/opal/dockerfiles-for-node-oracledb-are-easy-and-simple 참고
FROM oraclelinux:7-slim

RUN  yum -y install oracle-release-el7 oracle-nodejs-release-el7 && \
     yum-config-manager --disable ol7_developer_EPEL && \
     yum -y install oracle-instantclient19.3-basiclite nodejs && \
     rm -rf /var/cache/yum

WORKDIR /myapp
COPY toContainer/ /myapp/

RUN npm install

CMD exec node select1.js
```

Dockerfile과 동일 위치에 toContainer 디렉토리를 만든 후 컨테이너로 copy해야 할 파일 목록들을 옮겨놨다.
- package.json
- dbconfig.js
- select1.js

**package.json**
```json
{
  "name": "hee-test",
  "version": "1.0.0",
  "private": true,
  "description": "Test App",
  "dependencies": {
    "oracledb" : "^3.1"
  },
  "author": "Hee",
  "license": "MIT"
}
```
**dbconfig.js**
```js
module.exports = {
  user          : "system",
  password      : 'oracle',
  connectString : "172.17.0.2/xe"
};
```
**select1.js**
```js
var oracledb = require('oracledb');
var dbConfig = require('./dbconfig');

console.log('start to connect oracle-node!');

oracledb.autoCommit = true;

oracledb.getConnection(
  {
    user          : dbConfig.user,
    password      : dbConfig.password,
    connectString : dbConfig.connectString
  },
  function(err, connection)
  {
    if (err) {
      console.error(err.message);
      return;
    }


   var query = 'SELECT SEQ_NUM, USER_ID, USER_NAME, PROFILE FROM system.SHBEMP';

    connection.execute(query, function(err, result)
      {
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

function doRelease(connection)
{
  connection.release(
    function(err) {
      if (err) {
        console.error(err.message);
      }
    });
}
```

이렇게 구성한 뒤에 build, run 과정은 아래와 같다.

```sh
docker build -t nodeoracle:hee-test .
docker run nodeoracle:hee-test

#아래와 같이 출력
#====================================================================
start to connect oracle-node!
[ { name: 'SEQ_NUM' },
  { name: 'USER_ID' },
  { name: 'USER_NAME' },
  { name: 'PROFILE' } ]
[ [ 1, '19200889', 'HEEYE', 'graduate SKKU, join Shinhan Bank' ],
  [ 2, '19200900', 'CRUSH', 'My best singer XD' ] ]

# container에 들어가고 싶으면!
docker run -it nodeoracle:hee-test /bin/bash
```
