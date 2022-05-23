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
- 자세한 정리 👉 [**OSI 7 Layer의 4계층 전송계층**](https://github.com/Clary0122/AWS/blob/main/OSI%207%20Layers.md#4%EA%B3%84%EC%B8%B5---%EC%A0%84%EC%86%A1%EA%B3%84%EC%B8%B5transport-layer)
