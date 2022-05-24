> \<INDEX>
> 1. [TLS](#TLS)
> 2. [TCP와 UDP](#TCP와-UDP)

# TLS
## TLS란?
- TLS(Transport Layer Security)는 인터넷 상의 커뮤니케이션을 위한 개인 정보와 데이터 보안을 용이하게 하기 위해 설계되어 널리 채택된 보안 프로토콜.
- 주로 웹 사이트를 로드하는 웹 브라우저와 같이 **웹 응용 프로그램**과 **서버** 간의 커뮤니케이션을 **암호화**
- 이 프로토콜은 애플리케이션 계층(Layer 7)과 전송 계층(Layer 4) 간에 작동한다
- 국제 표준 기구 IETF(Internet Engineering Task Force)에 의해 제안 됨

## TLS와 SSL
- SSL(Secure Sockets Layer)의 발전 단계가 TLS.

## TLS와 HTTPS
- HTTPS는 HTTP프로토콜 상위에서 TLS 암호화를 구현한 것.
- 즉, HTTPS를 사용하는 웹 사이트는 TLS 암호화를 이용하는 것.

## TLS 역할
- **암호화** : 제 3자로부터 전송되는 데이터를 숨김
- **인증** : 정보를 교환하는 당사자가 요청된 당사자임을 보장
- **무결성** : 데이터가 위조되거나 변조되지 않았는지 확인

# TCP와 UDP
> - 전송계층의 데이터를 보내기 위해 사용하는 프로토콜  
>   👉 [**OSI 7 Layer의 4계층 전송계층**](https://github.com/Clary0122/AWS/blob/main/OSI%207%20Layers.md#4%EA%B3%84%EC%B8%B5---%EC%A0%84%EC%86%A1%EA%B3%84%EC%B8%B5transport-layer)
>   ![image](https://user-images.githubusercontent.com/79209568/170031468-44784593-9411-4f3d-9db2-40ea547a8848.png)
>   

## TCP (Transmission Control Protocol)
- 연결형 서비스로 **가상 회선 패킷 교환 방식**을 사용  
  
  ![image](https://user-images.githubusercontent.com/79209568/170036584-49123190-8246-4d82-b021-d8ae056fafc3.png)
- [3-way handshaking](#3-way-handshaking) 과정을 통해 연결을 설정하고 [4-way handshaking](#4-way-handshaking)을 통해 해제
- [흐름 제어와 혼잡 제어](#흐름제어-혼잡제어)를 한다.
- **높은 신뢰성을 보장한다.**
- UPD보다 **속도가 느리다.** (TCP의 흐름제어 혼잡제어 신뢰성보장을 위해 CPU를 사용하기 떄문)
- 전이중 방식이다. (송신을 하면서 동시에 수신도 할 수 있는 방식)
- 점대점 방식이다. (한 지점에서 다른 지점으로 직접 가는 방식)
- 1:1 통신

## UDP (User Datagram Protocol)
- 비연결형 서비스로 **데이터그램 방식**을 사용 (논리적인 경로 X)
  
  ![image](https://user-images.githubusercontent.com/79209568/170038475-c31c77ca-a96a-485e-8286-acb281e1a04f.png)

- 정보를 주고 받을 때 3-way handshaking 같은 신호절차를 거치지 않는다.
- UDP 헤더의 CheckSum 필드를 통해서 최소한의 오류만 검출
- 흐름 제어와 혼잡 제어를 하지 않는다.
- **신뢰성 낮다**
- TCP 보다 **속도가 빠르다.**
- 1:1, 1:N, N:M 통신

<hr>

> ### 3-way handshaking
> ![image](https://user-images.githubusercontent.com/79209568/170041309-f0b3637f-f27d-4faa-a29c-df50e32e0bda.png)
> - **\[Step 1]** Client가 Server에게 연결을 요청하는 **SYN 패킷**을 전송. Client는 SYN/ACK 응답을 기다리는 SYN_SENT 상태가 됨
> - **\[Step 2]** Server는 SYN요청을 받고 Client에게 요청을 수락한다는 **SYN/ACK 패킷**을 발송. Server는 Client에게 ACK 요청을 기다리는 SYN_RECEIVED 상태가 됨
> - **\[Step 3]** Client는 Server에게 **ACK 패킷**을 전송. 그 이후부터는 연결이 이루어지고 데이터가 오간다. 이 때 Server는 ESTABLISHED 상태가 됨

> ### 4-way handshaking
> ![image](https://user-images.githubusercontent.com/79209568/170042351-70e130f8-4fb1-4ee8-9796-50ab66884abe.png)
> - **\[Step 1]** Client가 Server에게 종료하겠다는 **FIN 패킷** 전송
> - **\[Step 2]** Server는 일단 **ACK 패킷**을 보내고 자신의 통신이 끝날 때 까지 기다리는데 이 상태가 CLOSE_WAIT 상태
> - **\[Step 3]** Server의 통신이 끝났으면 Client에게 연결이 종료되었다고 **FIN 패킷** 전송
> - **\[Step 4]** Client가 Server에게 **ACK 패킷**을 전송. 
그 후 TIME_WAIT 상태가 되는데, Sever로 부터 라우팅 지연이나 패킷 유실로 인한 재전송 등으로 FIN 패킷보다 늦게 오는 상황이 발생할 수 있기 때문에 일정시간동안 세션을 남겨 놓고 잉여 패킷을 기다리는 상태다.

> ### 흐름제어 혼잡제어
> - 흐름제어(Flow Control) : 데이터 송신자와 수신자의 데이터 처리속도를 조절. 수신자의 버퍼 오버플로우를 방지
> - 혼잡제어(Congestion Control) : 네트워크 내의 패킷 수가 넘치게 증가하지 않도록 방지. 정보의 소통량이 과다하면 패킷을 조금만 전송하여 혼잡 붕괴 현상이 일어나는 것을 막음
