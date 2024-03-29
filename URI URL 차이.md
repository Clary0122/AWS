# URI와 URL와 URN
![image](https://user-images.githubusercontent.com/79209568/168703229-8ada030a-9006-45ff-9f6d-1e023b3dcf6b.png)
- URI (Uniform Resource Identifier) **자원의 식별자**
- URL (Uniform Resource Locator) **위치**
- URN (Uniform Resource Name) **이름**

## URI - 식별
- 웹 기술에서 사용하는 논리적 또는 물리적 리소스를 식별하는 고유한 시퀀스 규약
- 인터넷에 있는 자원을 나타내느 유일한 주소
- 하위 개념으로 URL, URN 이 있음

## URL - 위치
- 흔히 **웹 주소**라고도 하며 컴퓨터 네트워크 상에서 리소스가 어디 있는지 알려주기 위한 URI 규약에 대한 형태.
- 컴퓨터 네트워크상의 자원을 모두 나타낼 수 있음

## URN - 이름
- 콘텐츠를 이루는 한 리소스에 대해, 그 리소스의 위치에 영향 받지 않는 유일무이한 이름 역할을 함.
- 리소스의 이름을 변경하지 않게 유지하는 한, 여러 종류의 네트워크 접속 프로토콜로 접근해도 문제 없음
- URL의 한계로 인해 나오게 됨
  ```
  <URL의 한계>
  현재 링크 : http://test.hello.com/20
  URL을 변경하고 싶어서 http://test.hello.com/test/20 로 변경
      ↓
  http://test.hello.com/20 이전 링크에 접속 시 페이지 접근 불가
  리소스가 옮겨지면 해당 URL을 더는 사용할 수 없다.
  그 시점 기존 URL이 가지고 있던 객체를 찾을 수 없게 된다.

### 예시
![image](https://user-images.githubusercontent.com/79209568/168702621-8a07f8ad-387b-4c10-bf94-9dfbb0e25caf.png)

- 두 주소는 모두 index.html을 가리키고 있음
- 첫 번째 : 웹서버의 실제 파일 위치를 나타내고 있기 때문에 **URI** 면서 **URL**
- 두 번째 : 실제로 index 파일이 웹서버에 존재하지 않기 때문에 **URL은 아님**. 하지만 서버 내부에서 이를 처리하여 결국 index.html을 가리키기 때문에 **URI**


![image](https://user-images.githubusercontent.com/79209568/168706806-ae3c95b3-d2a7-4981-9987-705fa54f8def.png)

### URI 구문
- **스키마 혹은 프로토콜** 
  - `http://`
  - 브라우저가 사용해야하는 프로토콜을 나타냄.
  - 일반적으로 **http, https**
- **도메인 이름(Authority)**
  - `www.google.co.kr`
- **포트**
  - `80`
  - 웹 서버 상의 리소스에 접근하는데 사용되는 기술적인 "문(gate)"를 나타냄
  - 리소스에 접근하기 위한 권한을 얻기 위해 웹 서버가 http 프로토콜의 표준 포트(http = 80, https = 443)를 사용하는 경우에는 생략됨. (아닌 경우 필수 입력!)
- **경로**
  - `clary/index.php`
  - 웹 서버 상의 리소스 경로
- **쿼리**
  - `?page=3`
  - 웹 서버에 제공되는 추가적인 파라미터
- **프래그먼트**
  - `#Rnfwoa`
  - 리소스 자체의 다른 부분을 가리키는 앵커 (북마크 된 지점)
