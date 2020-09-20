---
title: "Self-sign 인증서 만들기"
date: '2019-04-21 21:00:00'
category: dev
draft: false
---

스터디 때 실습한 내용을 command 위주로 정리했다.

### Self-sign 인증서 만들기 (실습)

$```openssl genrsa -out sign.key 2048```
> openssl을 이용해 개인키를 만듦

$```openssl rsa -noout -text -in sign.key```
> describe.
 modulus + privateExponent : private key
 modulus + publicExponent : public key

$```openssl req -new -key sign.key -out rootca.csr```
>You are about to be asked to enter information that will be incorporated
into your certificate request.<br/>
What you are about to enter is what is called a Distinguished Name or a DN.<br/>
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank. <br/>-----
Country Name (2 letter code) []:ko<br/>
State or Province Name (full name) []:Seoul<br/>
Locality Name (eg, city) []:Seoul<br/>
Organization Name (eg, company) []:Study<br/>
Organizational Unit Name (eg, section) []:group<br/>
Common Name (eg, fully qualified host name) []:study group<br/>
Email Address []:heeye.hwang@helloworld.com<br/>
Please enter the following 'extra' attributes<br/>
to be sent with your certificate request<br/>
A challenge password []:\*\*\*\*\*\*<br/>

> rootca.csr 이라는 public key 정보가 담긴 무언가가 생성

$```openssl x509 -req -days 3650 -extensions v3_ca -set_serial 1 -in rootca.csr -signkey sign.key -out rootca.crt```
>Signature ok
subject=/C=ko/ST=Seoul/L=Seoul/O=Study/OU=group/CN=study group/emailAddress=heeye.hwang@helloworld.com
Getting Private key
> sign.key로 rootca.csr에 서명하고 rootca.crt 인증서가 나옴!
