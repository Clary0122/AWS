# Cloud Front
> ##### .html, .css, .js 및 이미지 파일과 같은 정적 및 동적 웹 콘텐츠를 사용자에게 빠르게 배포하는 웹 서비스

- 엣지 로케이션이라고 하는 전 세계 데이터 센터 네트워크를 통해 콘텐츠를 제공

## 구성

![image](https://user-images.githubusercontent.com/79209568/170406283-3bd1e72e-dcd6-473c-9400-6b293774eba2.png)
#### 1. 오리진 서버 지정 (`Developer` → `CloudFront`)
- S3, EC2, 일반 HTTP서버
- 객체의 최종 버전을 저장
#### 2. 오리진 서버에 파일 업로드 (`Developer` → `CloudFront`)
- 웹 페이지, 이미지 및 미디어 파일이 일반적이지만 HTTP를 통해 제공될 수 있는 모든 것을 업로드 가능
#### 3. CloudFront 배포 생성 (`Developer` → `CloudFront`)
- 유저가 웹 사이트 또는 애플리케이션을 통해 파일을 요청할 때 파일을 가져올 오리진 서버를 CLoudFront에 알려줌

#### 4. 도메인 할당 (`CloudFront` → `Developer`)
- 콘솔에서 볼 수 있거나 프로그래밍 방식 요청(예 API 요청)에 대한 응답으로 반환되는 새 배포에 도메인 이름을 할당
- 대신 사용할 도메인을 추가 가능

#### 5. Edge Location에 전송 (`CloudFront` → `Edge Location`)
- 배포의 구성을 엣지 로케이션 또는 접속 지점(POP), 즉 클라우드 프론트가 파일 복사본을 캐시하는, 지리적으로 분산된 데이터 센터의 서버 모음으로 보냄.
- 생성 시 바로 배포되는 것이 아닌 **사용자의 요청이 있을 때** 보냄

## 데이터 전송 발생 과정

##### 1. 유저로부터 근거리 Edge Location 서버로의 요청 발생
##### 2. Edge Location 서버는 요청이 발생한 데이터에 대한 캐싱 여부 확인
![image](https://user-images.githubusercontent.com/79209568/170420001-c3b6f1bb-34a8-4cb5-b3eb-9b5b5496ada0.png)
##### 3-1. Edge Location 서버에 캐싱 데이터 존재
- 유저의 요청에 맞는 데이터 응답
##### 3-2. Edge Location 서버에 캐싱 데이터 없음
- 오리진 서버로 요청이 포워딩 됨
- 오리진 서버에서 요청 받은 데이터를 획득한 후 Edge Location 서버에 캐싱 데이터 생성
- 유저로 응답

## 실습
#### [👉 CF 생성 후 ALB 연동 - Route53 사용]()
