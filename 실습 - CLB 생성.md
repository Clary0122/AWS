> \<INDEX>
> - [CLB 생성](#CLB-생성)
>   - [Load Balancer 정의](#1.-Load-Balancer-정의)
>   - [보안-그룹-할당](2. 보안 그룹 할당)
>   - [보안 설정 구성](3. 보안 설정 구성)
>   - [상태 검사 구성](4. 상태 검사 구성)
>   - [EC2 인스턴스 추가](5. EC2 인스턴스 추가)
>   - [태그 추가](6. 태그 추가)
>   - [검토](7. 검토)
> - [EC2와 연결](EC2와 연결)
> - [접속 확인](접속 확인)
> - [정리](정리)

# CLB 생성
## 1. Load Balancer 정의
### 기본 구성
![image](https://user-images.githubusercontent.com/79209568/169178638-818cdb05-03b3-4f04-a1db-6ea97595c153.png)
#### 내부 Load Balancer 생성
- 체크하면 **내부 로드밸런서**, 해제하면 **인터넷 연결 로드밸런서**
  - 인터넷 연결 로드밸런서 : 공용 IP 주소. DNS Name은 노드의 공용 IP 주소로 공개적으로 확인가능, 인터넷을 통해 클라이언트의 요청을 라우팅 가능
  - 내부 로드밸런서 : 사설 IP 주소. DNS Name은 노드의 사설 IP 주소로 공개적으로 확인가능, 로드 밸런서용 VPC에 대한 액세스 권한이 있는 클라이언트의 요청만 라우팅 가능
#### 리스너 구성
- 로드 밸런서의 프로토콜과 포트와 라우팅 할 인스턴스의 프로토콜과 포트를 설정한다.
- **HTTPS(보안서버)** 리스너를 추가할 경우
  - ACM(AWS의인증서)는 인스턴스에는 사용하지 못하고 **ELB와 Cloud Front**에만 사용할 수 있다.
  - 그렇기 때문에 LB의 프로토콜은 HTTPS, 포트는 443으로 설정하고, 인스턴스의 프로토콜은 HTTP, 80으로 열어 놓는다.  
    
    ![image](https://user-images.githubusercontent.com/79209568/169179699-5bcda5f9-24fc-40eb-8353-51866907c9db.png)
  - 만약 사설 인증서를 쓰는 경우 : 인스턴스에 **사설 인증서**를 추가해주고 인스턴의 프로토콜과 포트를 HTTPS, 443으로 해 준다.  
    
    ![image](https://user-images.githubusercontent.com/79209568/169179856-8f84995d-8f57-4b62-abf6-3858b0f5ebb8.png)
### 서브넷 선택
![image](https://user-images.githubusercontent.com/79209568/169180193-c4e83dfd-064f-4e7c-8fe2-10aa5dd9dd55.png)
- 가용영역 a, c의 public 서브넷 선택
  - 인터넷 연결 로드밸런서를 선택했기 때문에 (여기서 private 서브넷을 선택하면 오류 남)  
    ![image](https://user-images.githubusercontent.com/79209568/169180959-a7167179-c596-440d-bfd2-2de776010a78.png)

## 2. 보안 그룹 할당
![image](https://user-images.githubusercontent.com/79209568/169181816-ad133cf4-41dc-4bab-8a34-e9511dc85773.png)

## 3. 보안 설정 구성
### 인증서 선택
#### 인증서 유형 : ACM에서 인증서 선택
![image](https://user-images.githubusercontent.com/79209568/169182012-658bbdce-4548-41ef-8d34-7f73e169e677.png)
- ACM 서비스에서 인증서 요청 선택
  - [ACM과 CA의 차이는 무엇인지?](#ACM과-CA)  
  
  ![image](https://user-images.githubusercontent.com/79209568/169185219-5b7354aa-32fb-4978-b3ae-f1a0f439e84c.png)
- 인증서 요청 : **퍼블릭 인증서 요청**   
  
  ![image](https://user-images.githubusercontent.com/79209568/169185308-6152bae6-97da-42fa-87f4-314b9d071ea3.png)

- 퍼블릭 인증서 요청  
  
  ![image](https://user-images.githubusercontent.com/79209568/169187009-c55a9c54-9224-4580-b75c-73064d87c800.png)
- 인증서 발급 완료 - 검증 대기 중이므로 `Route 53에서 레코드 생성` 클릭
  
  ![image](https://user-images.githubusercontent.com/79209568/169194351-683bad8e-969b-4340-94a1-410a9645dc6e.png)


- 레코드 생성  
  
  ![image](https://user-images.githubusercontent.com/79209568/169194502-ecf25130-4616-42db-9ebf-d3b6c62195cb.png)


- Route 52에 CNAME 유형의 레코드가 생성되어 있다  
  ![image](https://user-images.githubusercontent.com/79209568/169187347-545d8be3-6957-46e3-b0ab-d73213501f61.png)
- 발급도 완료  
  
  ![image](https://user-images.githubusercontent.com/79209568/169187476-035f539a-085a-479b-ae27-d7358b23aa7a.png)

### 암호 선택
- ELB는 SSL 협상 구성이 미리 정의된 보안 정책을 제공하거나 사용자 지정 보안 정책을 생성 가능
- 항상 최신의 보안 정책을 사용하는 것이 좋음  
  
![image](https://user-images.githubusercontent.com/79209568/169188126-ea3dc7e9-fb3d-491d-9962-3068f7e49907.png)

## 4. 상태 검사 구성
![image](https://user-images.githubusercontent.com/79209568/169188440-99b1358f-b2f3-459d-be0d-12077fd838d9.png)
### 고급 세부 정보
#### 응답 시간 초과
- 상태 검사로부터 응답을 수신할 때까지 기다리는 시간 (2-60초)
#### 간격
- 상태 검사 사이의 시간 (5~300초)
#### 비정상 임계값
- EC2 인스턴스를 비정상으로 선언하기 전까지 발생하는 연속적 상태 검사 실패 횟수
#### 정상 임계값
- EC2 인스턴스를 정상으로 선언하기 전까지 발생하는 연속적 상태 검사 성공 횟수   
  
  ![image](https://user-images.githubusercontent.com/79209568/169189031-5c203430-55f6-441a-8cb7-d789266789bd.png)

## 5. EC2 인스턴스 추가
![image](https://user-images.githubusercontent.com/79209568/169189140-bf6ccd92-f804-4d61-b824-d474e09cf11c.png)

- 가용영역 a에 퍼블릭 서브넷 내에 만들어 놓은 **KCY_ec2** 인스턴스 추가
#### 교차 영역 로드 밸런싱 활성화
- 교차 영역 로드 밸런싱은 Load Balancer에 활성화된 가용 영역의 대상 전반에서 트래픽을 균등하게 분산
- [교차 영역 로드 밸런싱 그림 및 설명](https://github.com/Clary0122/AWS/blob/main/1%20ELB.md#%EA%B5%90%EC%B0%A8%EC%98%81%EC%97%AD-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1)

#### Connection Draining 활성화
- 기존 트래픽이 플로우를 지속하도록 허용할 기간(초)

## 6. 태그 추가
- Key : `Name`
- Value : `KCY`

## 7. 검토
![image](https://user-images.githubusercontent.com/79209568/169189679-9cf0aeee-ebbe-4f2f-a8c4-bd7fbb00aec1.png)


## 8. 생성완료
![image](https://user-images.githubusercontent.com/79209568/169189639-3dfa5dd4-3e42-47df-902f-567c3743851e.png)

# EC2와 연결
![image](https://user-images.githubusercontent.com/79209568/169206005-694be8cc-3e4a-4196-bec6-82e0c437af1b.png)
> - CLB의 보안그룹의 인바둔드 규칙이 `HTTP 80포트`, `HTTPS 443포트`이므로 **EC2 인스턴스의 보안그룹 인바운드 규칙도 추가**해줘야 한다.
  
![image](https://user-images.githubusercontent.com/79209568/169206432-eb4b9d82-6896-4885-87a9-91b4f3ddfbf8.png)

# 접속 확인
- ALB 생성하며 설치했던 EC2의 아파치[✔과정보러가기](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20EC2%20%EB%B6%80%ED%95%98%ED%85%8C%EC%8A%A4%ED%8A%B8.md#ec2-%EC%84%9C%EB%B2%84%EC%97%90-apache-%EC%84%A4%EC%B9%98) 서버로 NLB를 통해 잘 접속되는지 확인
- NLB의 DNS Name 복사  
  ![image](https://user-images.githubusercontent.com/79209568/169209112-3bd2221b-c132-411e-9e88-7e5f4034c14f.png)

- 확인  
  ![image](https://user-images.githubusercontent.com/79209568/169210773-d52233f9-8d99-4ec9-b416-efcbc0627cb5.png)

- 뒤에 `health/healthcheck.html`을 추가해서 html 파일이 출력되는 지 확인

# 정리
![image](https://user-images.githubusercontent.com/79209568/169210031-5f88f06a-ecc2-486c-8175-24d022d4fa2b.png)


<hr>

> ### ACM과 CA
> #### ACM
> - TLS를 사용하여 공개적으로 신뢰할 수 있는 보안 웹이 필요한 기업 고객을 위한 인증서
> - ACM 인증서는 **ELB, CloudFront, API Gateway** 및 기타 통합 서비스에 배포 가능
> - 이러한 종류의 가장 일반적인 응용 프로그램은 **상당한 트래픽 요구 사항이 있는 안전한 공개 웹 사이트**이다.
> #### ACM Private CA
> - AWS 클라우드 내에 PKI(공개 키 인프라)를 구축한 기업 고객을 위한 것으로 **조직 내에서 개인적으로 사용하기 위한 것** - _인터넷이 아닌 조직내에서만 신뢰_
> - CA를 사용하면 내부사용자, 컴퓨터, 어플리케이션, 서비스, 서버 및 기타 장치를 인증하고 컴퓨터 코드에 서명하기 위한 인증서를 **조직 내부 요구에 맞게 직접 생성 가능**
