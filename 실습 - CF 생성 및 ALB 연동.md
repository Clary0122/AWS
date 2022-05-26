# Cloud Front 생성 및 ALB 연동
[👉 Cloud Front 정리](https://github.com/Clary0122/AWS/blob/main/Cloud%20Front.md)

> #### \<INDEX>
> 1. [Cloud Front 생성](#Cloud-Front-생성)
> 2. [Route53 연동](#Route53-연동)
> 3. [결과](#결과)

# Cloud Front 생성
## 오리진 설정
> - Cloud Front는 오리진에서 웹 콘텐츠를 가져와 이를 엣지 서버의 전 세계 네트워클 통해 최종 사용자에게 제공
> - 하나의 배포에 대해 최대 **25개의 오리진**을 설정할 수 있고 더 높은 할당량을 요청할 수 있음
> - 오리진을 삭제하려는 경우 캐시 동작을 편집하거나 삭제해야 함
### 원본 도메인
![image](https://user-images.githubusercontent.com/79209568/170433316-8eb1f035-4513-44b6-8bc8-740eca039d89.png)

- CF에서 오리진의 객체를 가져오려는 S3 버킷 또는 HTTP 서버의 DNS 도메인 이름
>   종류
>   |type|desc|
>   |--|---|
>   |S3 버킷|DOC-EXAMPLE-BUCKET.**s3**.**amazonaws.com**<br>- 다른 AWS 계정에서 버킷을 사용하려는 경우 버킷이 웹사이트로 구성되어 있지 않으면 이 형식으로 사용 `DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com`|
>   |웹 사이트로 구성된 Amazon S3 버킷|- 원본 도메인 리스트에서 S3 버킷 이름을 **선택하지 말고**, 정적 웹사이트 호스팅 엔드 포인트를 **직접 입력**|
>   |MediaStore 컨테이너|examplemediastore.**data.mediastore**.us-west-1.**amazonaws.com**|
>   |MediaPackage 엔드포인트|examplemediapackage.mediapackage.us-west-1.**amazonaws.com**|
>   |EC2 인스턴스|ec2-203-0-113-25.compute-1.**amazonaws.com**|
>   |ELB|example-load-balancer-1234567890.us-west-2.**elb**.**amazonaws.com**|
>   |자체 웹 서버|**https://www.example.com**|
  
#### 프로토콜
![image](https://user-images.githubusercontent.com/79209568/170433343-9b9a606f-f23e-4743-a1c3-fc96fc0fb891.png)

> - 원본 도메인을 ALB로 선택 시 나오는 속성
> - 오리진에 연결할 때 CloudFront에서 사용할 프로토콜(HTTP 또는 HTTPS)을 결정

- **HTTP만 해당**
- **HTTPS만 해당**
- **뷰어 일치**
  - CloudFront에서 뷰어 요청의 프로토콜에 따라 HTTP 또는 HTTPS를 사용하여 오리진과 통신

### 원본 경로 *-선택 사항*
- CF가 항상 오리진의 특정 디렉터리에서 콘텐츠를 요청하게 하려면 / 뒤에 디렉터리 경로를 입력
- 경로 끝에는 **/ 입력 X**

### 이름
![image](https://user-images.githubusercontent.com/79209568/170434970-d3168d75-e510-4441-8a0f-f7b73cd921e4.png)

- 현재 배포 내의 이 오리진을 고유하게 식별하는 문자열.
- 기본 캐시 동작에 추가 캐시 동작을 만들 경우, 이 이름을 사용해서 오리진을 식별한다.

### 사용자 지정 헤더 추가 *-선택 사항*
![image](https://user-images.githubusercontent.com/79209568/170434645-f201d99b-6120-4348-a91a-e3aefd139beb.png)

- CF가 오리진에 요청을 보낼 때마다 사용자 지정 헤더를 추가하도록 헤더 이름과 값을 지정
- 사용자가 CF를 우회하는지 알고 싶은 경우
- 2개 이상의 CDN을 사용하고 있는데 어떤 요청이 각 CDN에서 오는지에 대한 정보를 알고 싶은 경우 

### Origin Shield 활성화
![image](https://user-images.githubusercontent.com/79209568/170434932-c6fec43c-4a6e-47c3-9155-403f3517af0c.png)

- CF 캐싱 인프라에 계층을 하나 더 추가하여 오리진의 로드를 최소화하고 가용성을 높이며 운영 비용을 절감하는 데 도움을 줌

### 추가설정
![image](https://user-images.githubusercontent.com/79209568/170435035-3041cd5f-faf1-443d-851f-31a8751295a7.png)

<hr>

## 기본 캐시 동작
> - 웹 사이트에 있는 파일의 지정된 URL 경로 패턴에 대해 다양한 CF 기능을 구성할 수 있도록 한다.
> - 새 배포의 경우, 배포를 만들 때 지정한 오리진에 모든 요청을 자동으로 전달하는 기본 캐시 동작에 대한 설정을 지정
> - 이 배포를 만든 후 경로 패턴과 일치하는 객체에 대한 요청을 받았을 때 CF에서 응답하는 방식을 정의하는 추가 캐시 동작을 만들 수 있음
> - 추가 캐시를 생성하면 기본 캐시 동작은 항상 마지막에 처리 됨

### 경로 패턴
- 이 캐시 동작을 적용하려는 경로 지정
- 예) images/\*.jpg

### 자동으로 객체 압축
- 오리진에서 수신한 특정 파일을 뷰어에 전달하기 전에 CF에서 자동으로 압축

## 뷰어
![image](https://user-images.githubusercontent.com/79209568/170441883-d7cdd2b7-4c5e-40df-9103-7974e282d10f.png)

### 뷰어 프로토콜 정책
- 최종 사용자가 CF 엣지 로케이션의 콘텐츠를 액세스하는 데 사용하게 하려는 프로토콜 정책
- **HTTP and HTTPS** : 최종 사용자는 두 프로토콜 모두를 사용할 수 있음
- **Redirect HTTP to HTTPS** : 두 프로토콜 모두 사용할 수 있지만 HTTP 요청이 자동으로 HTTPS 요청으로 리다이렉션 됨
- **HTTPS Only**

### 허용된 HTTP 방법
- CF에서 오리진 처리 및 전달을 위한 HTTP 메서드를 지정
- **GET, HEAD** : 오리진에서 객체나 객체 헤더를 가져오기 위해서만 CF를 사용한다.
- **GET, HEAD, OPTIONS** : 오리진에서 객체나 객체 헤더를 가져오기 위해, 또는 오리진 서버에서 지원되는 옵션 목록을 가져오기 위해서만 CF를 사용한다.
- **GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE** : 객체 추가, 업데이트, 삭제 가능. 웹 양식에서 데이터를 제출하는 등의 POST 작업을 수행하기 위해 CF를 사용한다.
  - **캐시 HTTP 방법** : 최종 사용자가 OPTIONS 요청을 제출할 때, CF에서 오리진의 응답을 캐싱할지 

## 설정
#### 가격 분류
![image](https://user-images.githubusercontent.com/79209568/170442535-55574993-c201-4772-83b8-3a36e8198b87.png)
- 어느 지역 범위까지 Edge Location을 사용할 지 선택

#### AWS WAF 웹 ACL *-선택 사항*
- 원하지 않는 요청에 대한 방화벽을 설정
- 방화벽에 의해 거절된 요청은 403

#### 대체 도메인 이름(CNAME) *-선택 사항*
![image](https://user-images.githubusercontent.com/79209568/170445341-399b8e43-dfca-4b82-83bd-9021376b5453.png)

- CF 생성시 할당 받는 도메인 이름 대신 객체의 URL에 사용할 도메인 이름을 지정

#### 사용자 정의 SSL 인증서 *-선택 사항*
- **버지니아 북부** 리전에서 ACM 인증서 생성 후 Route53에서 레코드 생성
![image](https://user-images.githubusercontent.com/79209568/170444285-aa899f67-0a0f-41d9-bdc9-fb60434b97d9.png)

#### 기본값 루트 객체 *-선택 사항*
- 특정 객체가 아닌 배포 URL을 요청할 때 CF가 오리진에 요청할 객체 정보
- 예) `http://www.example.com/` 으로 요청을 보낸 경우 오리진에서는 지정된 객체(index.html)을 응답

## 배포완료
![image](https://user-images.githubusercontent.com/79209568/170452056-10ccd6f8-26b7-4736-b457-2b8e14b3fbee.png)


# Route53 연동
- Route53에서 대체 도메인 이름(CNAME)으로 설정했던 `s3static.kcynds.shop`의 호스팅 영역을 생성 [생성과정](https://github.com/Clary0122/AWS/blob/main/S3%20%EC%A0%95%EC%A0%81%EC%9D%B8%20%EC%9B%B9%20%ED%98%B8%EC%8A%A4%ED%8C%85.md)
## CF 접속 레코드 생성
- `값`에는 CF의 도메인 이름을 넣는다
#### CNAME 레코드
![image](https://user-images.githubusercontent.com/79209568/170452900-2e558c94-27d5-4d41-80c6-2593f93dcf5b.png)
#### A 레코드
![image](https://user-images.githubusercontent.com/79209568/170455638-cc166c1a-77c9-41a8-9253-c84bf17cae12.png)


<hr>

# 결과
- `cf.s3static.kcynds.shop` 혹은 `cf2.s3static.kcynds.shop`로 접속

### 차이점
#### CNAME 레코드
![image](https://user-images.githubusercontent.com/79209568/170455092-24e76cb0-1b9b-4633-8eb6-f09a9dd03f1c.png)

#### A 레코드
![image](https://user-images.githubusercontent.com/79209568/170455118-5beb74c8-4f5b-409c-9590-fc16cb2cffb8.png)
