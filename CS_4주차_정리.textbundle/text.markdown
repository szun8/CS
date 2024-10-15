# 2.2 TCP와 UDP

## TCP : Transmission Control Protocol
### 특징
- 연결형 서비스
- 가상회선방식
	- 패킷을 전송할 경로인 가상 회선을 설정해 모든 패킷을 같은 경로로 전송
	- 전송순서 보장
- 수신 여부 확인
	- 손실이 없음을 보장하여 신뢰성 높음
	- 유기적인 데이터
- 1:1 통신
- 비교적 느린 통신 속도

### HandShaking
- 송신부와 수신부의 연결제어 및 관리를 위한 플래그 값 존재
![](assets/100B92DE-2D14-48D2-90C7-39CCDD4D9E2A.jpg)
- ### 연결 생성 : 3-Way Handshaking
	![](assets/5C8BA030-E2B8-4084-ADF4-B6396239E01C.jpg)
- RTT : Round Trip Time
	timeout의 임계점  
	- 이쯤이면 ack가 와야한다는 가변적인 시간 기준
- ### 연결 종료 : 4-Way HandShaking
	![](assets/CAA36DD7-AD7F-4FA9-B31D-92164F5B4AFD.jpg)
	- Time_Wait 상태
		- 패킷 유실 대비
![](assets/B24B09FB-48B8-4544-8F96-38DAAF85A998.jpg)
			4wayHandShaking  
			- 송신부가 서버로부터 받은 FIN 메시지에 응답하기 위해 ACK 메시지를 보내고 TimeWait 상태가 되고 일정 시간이 지나면 Closed상태가 된다  
			=> 이때 Time Out 상태를 유지하는 이유  
			1. FIN 메시지 수신보다 지연되어 발생하는 패킷 유실 대비  
			2. 수신부에 ACK메시지가 제대로 전달되지 않아 연결해제가 이뤄지지 않는 경우도 대비
		- 손실된 ACK에 대한 재전송 보장
![](assets/9DA0DE22-B420-4281-9D34-F1AB44B6908F.png)
	- TCP Keep Alive 패킷
		- 연결 유지 상태 확인
	- 좀비 커넥션
		- 둘 중 한쪽이 Closed되지 않고 계속 살아있는 상태

### 제어방법
- #### 흐름제어 (1:1)
	- 데이터 송신부와 수신부에서 데이터 처리 속도의 차이로 인해 생기는 데이터 손실을 방지하는 방법
		- 정지-대기
			- ACK
		- 슬라이딩 윈도우
			- 윈도우 크기
- #### 혼잡제어 (1:N)
	![](assets/274C5B41-BA12-4814-AD95-773FCF87D46F.png)
	- 송신부의 데이터 전달 속도와 네크워크 속도 차이로 데이터 손실이 발생하는 것을 방지하기 위한 방법

		- AIMD : Additive Increase Multiplicative Decrease
![](assets/31D9E82E-E137-4265-AFE3-3E7A1E342AAB.png)
		- slow start
![](assets/685871C8-4E73-4D17-911D-0A7B9FE7AF51.png)
			지수함수	
			- +. 혼잡 회피
				slow start로 cwnd를 계속 증가시켰다가 임계점에 도달하면 선형 증가 (확실히 발생되기 전에 회피 기법)
				- ssthresh
				- ```  
				  if (timeOut)  
				     sstresh = cwnd /2;  
				     cwnd = 1;  
				  ```
		- 빠른 회복
			- ```  
			  if ( isCongestion)   
			        cwnd /= 2;   
			  ```
				cwnd를 반으로 줄인 다음 선형적으로 증가(AMID방식으로)
		- 빠른 재전송
			- ```  
			  if (duplicate ACKCnt == 3)   
			           cwnd /= 2;  
			  ```
		- 혼합 제어 정책
			- TCP Tahoe
![](assets/55D234F4-77E0-4B14-BBD0-6F95340FDF31.jpg)
				- slow start + congestion avoidance
			- TCP Reno
![](assets/D6ADCE4C-04D8-4512-966D-83483CFA8CDE.jpg)
				- slow start
					- 3 Duplicate ACK
						- cwnd /= 2;  
						  ssthresh = cwnd;
					- time out
						- cwnd = 1
- 오류제어
	- 데이터에 오류 또는 유실이 발생할 때 데이터의 신뢰성을 보장하기 위해 오류를 제어하기 위한 방식
	- 데이터 오류 || 유실 발생의 경우
		- NAK
		- 3 Duplicate ACK
		- time out
	- 방법
		- 정지-대기
			- ARQ : Automatic Repeat Request 재전송 요청
		- Go-Back-N ARQ
			- 받지 못한 패킷 번호를 ACK와 함께 보내 송신부에 유실 처리를 알린다.
			- 유실된 N부터 다시 전송
		- Selective-Repeat ARQ
			- 유실된 N만! 다시 전송
			- 재정렬 과정

## UDP : User Datagram Protocol

### 특징
- 비연결형 서비스
- 데이터그램 방식
	- 순서 미보장
- 수신 여부 미확인
	- 신뢰성이 낮음
	- 독립적인 데이터
- 1:1, 1:N, N:N 통신 가능
- 비교적 빠른 통신 속도
	- 게임에서는 주로 UDP를 사용

### 오류 검출
- CheckSum
- 그래도 완벽하진 않긴 해

# 2.3 HTTP

## HTTP

### 특징
- 비연결성
	- HTTP Keep Alive
		응용 계층에서 설정하는 값이기 때문에 tcp keep alive랑 같이 사용하는 것이 통상적  
		  
		tcp keep alive는 os  
		http keep alive는 응용 소프트웨어에서 관리한다.  
		  
		만약 http keep alive는 켜놓고 tcp keep alive는 꺼놨다면,  
		  
		**응용 계층**에서는 **tcp의 연결상태**가 어떤 이유로든 꺼져있는지 확인을 못하니까  
		( = 네트워크 장애)  
		  
		켜져있다는 상태로 request를 보내지만 통신 불가로 해당 패킷은 유실이 된다.
		- 하나의 TCP 연결에 대해 여러 요청을 보내기 위해 설정하는 헤더의 값
- stateless
	- 서버에서 클라이언트의 상태를 저장하지 않는 것
		- MMORPG같은 게임서버에서는 stateful
![](assets/58459A2A-2965-4091-B83C-5E11C7CB87E8.png)
		- 실시간 상호작용으로 빠른 처리 후 클라에게 뿌려줘야함
			- 언제 DB가서 정보가져오고 있니
			로그인할때부터 사용자 정보를 DB에서 메모리로 가져온다음 실시간으로 정보를 조회한다  
			  
			- FPS의 존버와 여포중 여포가 줄곧 성공하는 이유  
			  
			- 상태(클라이언트 정보)를 서버가 계속 저장하고 있어서 이전 행동에 대한 추가 데이터가 필요없으므로 바로 다음 행동 처리 가능
	- cookie
		- 클라이언트 측에 저장되는 데이터 파일
		- Request시 헤더에 저장되어 전송됨
		- 보안
			- 암호화
			- Secure 플래그 : HTTPS 연결에서만 전송되도록 설정된 쿠키
			- HttpOnly 플래그 : JavaScript에서 접근할 수 없도록 설정된 쿠키
	- session
		- 서버와 클라이언트의 연결정보를 저장 및 관리

### version
- 1.0
	- 기본 keepAlive : close
- 1.1
	- keepAlive가 디폴트로 활성화되어있으므로 원하지 않는다면 헤더 connection : close 명시

### RequestMessage
![](assets/FBFB824D-DED1-48FE-B3B8-ADACF303C8CF.jpg)

### ReponseMessage
![](assets/0AE05A04-C6D7-4DF4-82FF-8E4F4AA6FBE5.jpg)

### connection 종류
- persistent
- parallel
- pipelined

## HTTPS = HTTP + 보안

### SSL/TLS 보안 계층을 추가하여 메시지를 암호화해 서버에 보내고 응답받는다

### TLS Handshaking
- 대칭키
- 공개키
- cf. UDP는 DTLS라는 프로토콜을 사용
	datagram transport layer Security

## Proxy

### Forward

### Reverse

# 2.4 REST

## REST : Representational State Transfer

### 특징
- 일관된 인터페이스
- 클라-서버 구조
- 무상태성
- 캐싱 가능
- 자체 표현 구조
- 계층형 구조

### 자원
- URI로 자원 식별
![](assets/34BAB518-611E-4D9C-AC56-523848A682E4.jpg)
	- URN
	- URL

### 행위 : CRUD -> HTTP method
- GET
- POST
- PUT
- Delete

### 표현
- JSON, Xml

## REST API
![](assets/BCEB2303-31B1-4BF1-AAC6-F38F9D2860C4.jpg)

### restful하다…

### cf. API : Application Programming Interface
toss - api - 우린은행 서버(고객 계좌 정보)  
  
쿠키런 - api - 카카오톡 서버(계정정보)
