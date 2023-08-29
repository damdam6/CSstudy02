# WEEK04_HTTP

---

[https://thebook.io/080326/0043/](https://thebook.io/080326/0043/)

- 앞서 설명한 전송 계층 위에 있는 애플리케이션 계층
- 웹 서비스 통신에 사용되며 HTTP/3까지 도달(HTTP/1.0 ~ HTTP/3)

## 2.5.1 HTTP/1.0

- HTTP/1.0은 기본적으로 한 연결 당 하나의 요청을 처리하도록 설계
- RTT 증가를 불러오게 됨

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled.png)

- 서버로부터 파일을 가져올 때마다 TCP의 3-웨이 핸드셰이크를 계속해서 열어야 하므로 RTT 증가

** RTT : 패킷이 목적지에 도달하고 나서 다시 출발지로 돌아오기까지 걸리는 시간(패킷 왕복 시간)

### RTT 증가 해결 방법

- 연결 시마다 RTT가 증가하므로 서버에 부담이 많이 가고 사용자 응답 시간이 길어짐
- 이미지 스프리팅, 코드 압축, 이미지 Base64 인코딩 사용

**이미지 스플리팅**

- 많은 이미지를 다운로드 받으면 과부하가 걸리므로 많은 이미지를 합쳐 있는 하나의 이미지를 다운 받고, 이를 기반으로 background-image의 position을 이용하여 이미지를 표기하는 방법

```css
#icons>li>a {
    background-image: url("icons.png");
    width: 25px;
    display: inline-block;
    height: 25px;
    repeat: no-repeat;
}
#icons>li:nth-child(1)>a {
    background-position: 2px -8px;
}
#icons>li:nth-child(2)>a {
    background-position: -29px -8px;
}
```

→ 하나의 이미지인 icons.png를 기반으로 background-position을 통해 2개의 이미지를 설정

**코드 압축**

- 코드를 압축해서 개행 문자, 빈칸을 없애서 코드의 크기를 최소화하는 방법

```jsx
const express = require('express')
const app = express()
const port = 3000
app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(port, () => {
    console.log(`Example app listening on port ${port}`)
})
```

위의 코드를 아래로 변경(개행 문자, 띄어쓰기 등이 사라져 코드 용량이 줄어듦)

```jsx
const express=require("express"),app=express(),port=3e3;app.
get("/",(e,p)=>{p.send("Hello World!")}),app.listen(3e3,()=>{console.
log("Example app listening on port 3000")});
```

**이미지 Base64 인코딩**

- 이미지 파일을 64진법으로 이루어진 문자열로 인코딩하는 방법
- 장점 : 서버와의 연결을 열고 이미지에 대해 서버에 http 요청을 할 필요가 없음
- 단점 : Base64 문자열 변환 시 37% 정도 크기가 커짐

** 인코딩 : 정보의 형태나 형식을 표준화, 보안, 처리 속도 향상, 저장 공간 절약 등을 위해 다른 형태나 형식으로 변환하는 처리 방식

## 2.5.2 HTTP/1.1

- HTTP/1.0에서 발전
- 매번 TCP 연결 X, 한 번 TCP 초기화 이후 keep-alive 옵션으로 여러 파일 송수신 가능
- 1.0에서도 keep-alive가 있었으나 표준화 X

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%201.png)

- 한 번 TCP 3-웨이 핸드셰이크 발생 시 다음부터는 발생 X
- 문서에 포함된 다수의 리소스를 처리하려면 요청할 리소스 개수에 비례하여 대기 시간 증가

### **HOL Blocking(Head OF Line Blocking)**

- 네트워크에서 같은 큐에 있는 패킷이 그 첫 번째 패킷에 의해 지연될 때 발생하는 성능 저하 현상

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%202.png)

→ image.jpg와 styles.css, data.xml을 다운로드 받을 때 보통은 순차적으로 잘 받아지지만 image.jpg가 느리게 받아진다면 그 뒤에 있는 것들이 대기하게 되며 다운로드가 지연되는 상태가 됨

### **무거운 헤더 구조**

- 헤더에 쿠키 등 많은 메타데이터가 포함되고 압축이 되지 않아 무거움

## 2.5.3 HTTP/2

- SPDY 프로토콜에서 파생된 HTTP/1.x보다 지연시간을 줄이고 응답 시간을 빠르게 가능
- 멀티플렉싱, 헤더 압축, 서버 푸시, 요청의 우선순위 처리를 지원

### 멀티플렉싱

- 여러 개의 스트림을 사용하여 송수신
- 특정 스트림의 패킷이 손실되었다고 해도 해당 스트림에만 영향을 미치고 나머지 스트림은 멀쩡하게 동작 가능

** 스트림 : 시간이 지남에 따라 사용할 수 있게 되는 일련의 데이터 요소를 가리키는 데이터 흐름

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%203.png)

→ 하나의 연결 내 여러 스트림의 모습

1. 병렬적인 스트림(stream)들을 통해 데이터 서빙
2. 애플리케이션에서 받아온 메시지를 독립된 프레임으로 조각내어 송수신 후 다시 조립하여 데이터 송수신

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%204.png)

→ 단일 연결을 사용하여 병렬로 여러 요청을 받을 수 있고 응답을 줄 수 있음

**HTTP/1.x에서 발생하는 HOL Blocking 해결 가능**

### 헤더 압축

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%205.png)

- 허프만 코딩 압축 알고리즘을 사용하는 HPACK 압축 형식

**허프만 코딩**

- 문자열을 문자 단위로 쪼개 빈도수를 세어 빈도가 높은 정보는 적은 비트 수를 사용하여 표현, 빈도가 낮은 정보는 비트 수를 많이 사용하여 표현해 전체 데이터 표현에 필요한 비트 수 감소

### 서버 푸시

- HTTP/1.1 에서는 클라이언트가 서버에 요청해야 파일 다운 가능
- HTTP/2는 클라이언트 요청 없이 서버가 바로 리소스 푸시 가능

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%206.png)

- html에는 css나 js 파일이 포함되기 마련인데 html을 읽으면서 그 안에 들어있던 css 파일을 서버에서 푸시하여 클라이언트에 먼저 줄 수 있음

## 2.5.4 HTTPS

- HTTP/2는 HTTPS 위에서 동작
- HTTPS : 애플리케이션 계층과 전송 계층 사이에 신뢰 계층인 SSL/TLS 계층을 넣은 신뢰 가능한 HTTP 요청 → 통신 암호화

### SSL/TLS

- SSL(Secure Socket Layer)은 SSL 1.0부터 시작해서 SSL 2.0, SSL 3.0, TLS(Transport Layer Security Protocol) 1.0, TLS 1.3까지 버전 발전
- 마지막으로 TLS로 명칭이 변경되었으나, 보통 이를 합쳐 SSL/TLS로 통칭
- 전송 계층에서 보안을 제공하는 프로토콜
- 클라이언트와 서버 통신시 SSL/TLS를 통해 제3자가 메시지를 도청 및 변조 불가하도록 처리

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%207.png)

- 공격자가 서버인 척하며 사용자 정보를 가로채는 네트워크 상의 ‘인터셉터’ 방지 가능
- 보안 세션을 기반으로 데이터를 암호화하며 보안 세션이 만들어질 때 인증 메커니즘, 키 교환 암호화 알고리즘, 해싱 알고리즘 사용

**보안 세션**

- 보안이 시작되고 끝나는 동안 유지되는 세션
- SSL/TLS는 핸드셰이크를 통해 보안 세션을 생성하고 이를 기반으로 상태 정보 등을 공유

** 세션 : 운영체제가 어떠한 사용자로부터 자신의 자산 이용을 허락하는 일정 기간. 사용자는 일정 시간동안 응용 프로그램, 자원 등 사용

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%208.png)

→ 클라이언트와 서버가 키를 공유하고 이를 기반으로 인증, 인증 확인 등의 작업이 일어나는 단 한번의 1-RTT 생성 후 데이터 송수신

1. 클라이언트에서 사이퍼 슈트(cypher suites)를 서버에 전달
2. 받은 사이퍼 슈트의 암호화 알고리즘 리스트를 제공할 수 있는 지 확인
3. 3.제공 가능하면 서버에서 클라이언트로 인증서를 보내는 인증 메커니즘 시작
4. 해싱 알고리즘 등으로 암호화된 데이터 송수신 시작

사이퍼 슈트

- 프로토콜, AEAD 사이퍼 모드, 해싱 알고리즘이 나열된 규약
    - TLS_AES_128_GCM_SHA256
        
         // TLS : 프로토콜, AES_128_GCM : 사이퍼 모드, SHA256 : 해싱 알고리즘
        
    - TLS_AES_256_GCM_SHA384
    - TLS_CHACHA20_POLY1305_SHA256
    - TLS_AES_128_CCM_SHA256
    - TLS_AES_128_CCM_8_SHA256

AEAD 사이퍼 모드

- AEAD(Authenticated Encryption with Associated Data)는 데이터 암호화 알고리즘
    
    ex) AES_128_GCM : 128비트의 키를 사용하는 표준 블록 암호화 기술과 병렬 계산에 용이한 암호화 알고리즘 GCM이 결합된 알고리즘
    

**인증 매커니즘**

- CA(Certificate Authorities)에서 발급한 인증서를 기반을 이루어짐
- CA에서 발급한 인증서는 안전한 연결을 시작하는 데 있어 필요한 ‘공개 키’를 클라이언트에 제공하고 사용자가 접속한 ‘서버가 신뢰’할 수 있는 서버 보장
- 인증서는 서비스 정보, 공개키, 지문, 디지털 서명으로 이루어져 있음
- 아무 기업이나 할 수 있는 것이 아니고 신뢰성이 엄격하게 공인된 기업들만 참여 가능

CA 발급 과정

- CA 인증서를 발급 받으려면 자신의 사이트 정보와 공개키를 CA에 제출해야 함
- 이후 CA는 공개키를 해시한 값인 지문(finger print)을 사용하는 CA의 비밀 키 등을 기반으로 CA 인증서 발급

** 개인키 : 비밀키라고도 하며, 개인이 소유하고 있는 키이자 반드시 자신만이 소유해야 하는 키

** 공개키 : 공개되어 있는 키

**암호화 알고리즘**

- 키 교환 암호화 알고리즘으로는 대수곡선 기반의 ECDHE(Elliptic Curve Diffie-Hellman Ephermeral) 또는 모듈식 기반의 DHE(Diffic-Hellman Ephermeral) 사용
- 둘 다 디피-헬만(Diffie-Hellman) 방식을 근간으로 생성됨

디피-헬만 키 교환 암호화 알고리즘

- 디피-헬만 키 교환(Diffie-Hellman key exchange) 암호화 알고리즘은 암호키를 교환하는 방법

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%209.png)

→ g와 x와 p를 안다면 y는 구하기 쉽지만 g와 y와 p만 안다면 x를 구하기는 어렵다는 원리에 기반한 알고리즘

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%2010.png)

1. 처음에 공개 값 공유 후 각자의 비밀 값과 혼합 후 혼합 값을 공유
2. 각자의 비밀 값과 다시 혼합
3. 공통의 암호키인 PSK(Pre-Shared Key)가 생성됨

→ 클라이언트와 서버 모두 개인키와 공개키를 생성하고, 서로에게 공개키를 보내고 공개키와 개인키를 결합하여 PSK가 생성된다면, 악의적인 공격자가 개인키 또는 공개키를 가지고도 PSK가 없으므로 아무것도 할 수 없음

**해싱 알고리즘**

- 해싱 알고리즘은 데이터를 추정하기 힘든 더 작고, 섞여 있는 조각으로 만드는 알고리즘
- SSL/TLS는 해싱 알고리즘으로 SHA-256 알고리즘과 SHA-384 알고리즘 사용

SHA-256

- 해시 함수의 결과값이 256비트인 알고리즘이며 비트 코인을 비롯한 많은 블록체인 시스템 사용
- 해싱을 해야 할 메시지에 1을 추가하는 등 전처리 후 전처리된 메시지를 기반으로 해쉬 반환

** 해시 : 다양한 길이를 가진 데이터를 고정된 길이를 가진 데이터를 매핑(mapping)한 값

** 해싱 : 임의의 데이터를 해시로 바꿔주는 일이며 해시 함수가 이를 담당

** 해시 함수 : 임의의 데이터를 입력으로 받아 일정한 길이의 데이털오 바꿔주는 함수

### SEO에도 도움이 되는 HTTPS

- 구글은 SSL 인증서를 강조해왔고 사이트 내 모든 요소가 동일하다면 HTTPS 서비스를 하는 사이트가 그렇지 않은 사이트보다 SEO 순위가 높을 것이라고 공식적으로 밝힘
- SEO(Search Engine Optimization) : 검색엔진 최적화를 뜻하며 사용자들이 검색엔진으로 웹 사이트를 검색 시 그 결과를 페이지 상단에 노출시켜 많은 사람이 볼 수 있도록 하는 방법
- 서비스를 운영한다면 SEO 관리는 필수(사이트 유입을 위해)
- 캐노니컬 설정, 메타 설정, 페이지 속도 개선, 사이트맵 관리 등 존재

**캐노니컬 설정**

```xml
<link rel="canonical" href="https://example.com/page2.php" />
```

- 사이트 link에 캐노니컬을 설정

**메타 설정**

- html 파일의 가장 윗부분인 메타를 잘 설정해야 함

**페이지 속도 개선**

- 사이트 접속 시 시간이 오래 걸리면 이용량 감소

**사이트맵 관리**

- 사이트맵(sitemap.xml) 정기 관리는 필수
- 사이트맵 제너레이터를 사용하거나 직접 코드를 만들어 구축해도 됨

```xml
<?xml version="1.0" encoding="utf-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<url>
<loc>http://kundol.co.kr/</loc>
<lastmod>수정날짜</lastmod>
<changefreq>daily</changefreq>
<priority>1.1</priority>
</url> 
</urlset>
```

### HTTPS 구축 방법

1. CA에서 구매한 인증키를 기반으로 HTTPS 서비스 구축
2. 서버 앞단의 HTTPS를 제공하는 로드밸런서 두기
3. 서버 앞단에 HTTPS를 제공하는 CDN 두기

## 2.5.5 HTTP/3

- HTTP/1.1 및 HTTP/2와 함께 World Wide Web에서 정보를 교환하는 데 사용되는 HTTP의 세 번째 버전
- TCP 위에서 돌아가는 HTTP/2와는 달리 HTTP/3은 QUIC 계층 위에서 돌아감
- TCP 기반이 아닌 UDP 기반으로 돌아감

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%2011.png)

- HTTP/2의 장점인 멀티플렉싱을 가지고 있으며 초기 연결 설정 시 지연 시간 감소 장점 존재

### 초기 연결 설정 시 지연 시간 감소

- QUIC은 TCP를 사용하지 않으므로 통신 시작 시번거로운 3-웨이 핸드셰이크 과정 X

![Untitled](WEEK04_HTTP%20ac24c60dc7c54e748234e18705f409f4/Untitled%2012.png)

- QUIC은 첫 연결 설정에 1-RTT만 소요
- 클라이언트가 서버에 어떤 신호를 한 번 주고, 서버도 거기에 응답하기만 하면 바로 본 통신 시작
- QUIC은 순방향 오류 수정 매커니즘(FEC, Forward Error Correction)이 적용
- 전송 패킷 손실 시 수신 측에서 에러 검출 후 수정하는 방식
- 열악한 네트워크 환경에서도 낮은 패킷 손실률 자랑ㄴㅋ