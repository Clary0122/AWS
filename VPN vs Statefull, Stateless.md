# VPN vs Stateful, Stateless 

## Stateful, Stateless
- 클라이언트의 상태 정보를 서버에 저장하느냐 마느냐로 구분 

### Stateful
- 서버와 클라이언트 간 세션의 상태에 기반하여 클라이언트에 응답을 보냄
- 세션 상태를 포함한 클라이언트와의 세선정보를 서버에 저장
- 대표적 : TCP 

### Stateless
- 서버의 응답이 클라이언트와의 상태와 독립적
- 해당 구조에서 서버는 단순히 요청이 들어오면 응답을 보내는 역할만 수행, 세션 관리는 클라이언트에 책임
- 클라이언트와의 세션정보를 기억할 필요 없으므로 따로 외부 DB에 저장하여 관리
- scaling이 자유로움
- 대표적 : HTTP

#### 패킷필터링 기술
- Stateless(정적)
  - 정책(ACL)을 보고 패킷을 보냄
  - 패킷 헤더만 검사
- Stateful(동적) 
  - 한 번 허용된 패킷은 잠깐 세션을 저장하여 후속 패킷들은 그대로 통과
  - app 계층까지 분석, 네트워크 연결 상태 추적 (더 넓은 트래픽을 볼 수 있어서 다양한 계층 공격에 대응가능 / 속도는 떨어짐)   
  ![image](https://user-images.githubusercontent.com/79209568/167970347-a5f76a87-ba0d-47b1-8737-1d9b8fb87a49.png)

## VPN
- 공중망을 마치 전용망처럼 사용하는 기술 - 가상사설망
- 비용절감, 확장성, 암호화(보안성)  
- 터널링 : 보안효과를 주기위한 기법. 페이로드(터널링 되는 데이터)는 캡슐화되어 전송
![image](https://user-images.githubusercontent.com/79209568/167970635-b2a075c0-b48f-467a-b334-1c0f65b437d1.png)
