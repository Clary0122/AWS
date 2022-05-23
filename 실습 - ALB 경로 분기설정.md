> \<**INDEX**>
> 1. [새 EC2 생성](#새-EC2-생성)
> 2. [EC2에 apache 설치](#EC2에-apache-설치)
> 3. [대상그룹, 보안그룹 설정](#대상그룹-보안그룹-설정)
> 4. [리스너 규칙 추가](#리스너-규칙-추가)
> 5. [결과](#결과)

> \<**실습목표**>
> ![image](https://user-images.githubusercontent.com/79209568/169736816-448984bd-7bf1-42a8-b31f-b7f9b63fec8a.png)
> - HTTP:80 같은 포트로 보내고 리스너 규칙으로 다른 경로로 갈 때 분기하도록 설정하기 

# 새 EC2 생성
## EC2 속성
- 플랫폼 : Amazon Linux
- 인스턴스 유형 : t2.nano
- VPC : KCY vpc
- 서브넷 : KCY-public-1c
- 키 페어 : **새로운 키페어** KCY_keypair_2
- 보안그룹 : KCY-ec2-2-sg

## EIP 설정
- `탄력적 IP 주소 할당` 클릭 후 EIP 생성  
  ![image](https://user-images.githubusercontent.com/79209568/169728935-d422ba17-d1fa-4957-b2b2-dcb8a00a1300.png)

- 생성한 EIP KCY-ec2-2 인스턴스와 연결  
  ![image](https://user-images.githubusercontent.com/79209568/169728918-49b1379d-36db-4777-a27b-b3adc07bc8cf.png)

# EC2에 apache 설치
- [👉**아파치 설치 과정**](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20EC2%20%EB%B6%80%ED%95%98%ED%85%8C%EC%8A%A4%ED%8A%B8.md#ec2-%EC%84%9C%EB%B2%84%EC%97%90-apache-%EC%84%A4%EC%B9%98)

> ### mobaXterm에서 EC2 접속하는 법
> - `Session` - `SSH` 버튼 클릭 후 Remote host에 EC2의 퍼블릭 IP(EIP)를 넣어 준다.  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/169729861-2a7a05a7-8540-4225-942d-efc88a1a93c6.png)
> - `Specify username`에 **ec2-user**을 입력하고 `Advanced SSH settings` - `Use private key`에 생성했던 키페어를 넣으면 다음번에 직접 ec2-user로 로그인하지 않아도 접속 가능  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/169730332-42c29165-4cf8-460b-a86b-64a9ba3cb11b.png)
> - /var/www/html 에 /health/healthcheck2.html 을 생성
>   
>   ![image](https://user-images.githubusercontent.com/79209568/169733526-becb08e3-399f-44c1-b037-849dec22b93f.png)

# 대상그룹, 보안그룹 설정
- EC2-2의 보안 그룹에 alb-sg를 추가  
  ![image](https://user-images.githubusercontent.com/79209568/169731382-143b2db1-efb0-4213-8f1d-b107da5fbedf.png)

- ALB에 새로운 대상그룹을 추가하고 EC2-2를 추가
  ![image](https://user-images.githubusercontent.com/79209568/169733571-c12c398d-d549-4784-9ff9-59c6f5696a9a.png)  
  ![image](https://user-images.githubusercontent.com/79209568/169733589-9fba29c8-14c8-44d2-9559-81f3dfb49863.png)

# 리스너 규칙 추가
- `HTTP:80`으로 들어오기 때문에 규칙에 추가해준다.
- 경로가 `/health/healthcheck.html`일 때 KCY-tg(EC2-1)로 연결
- 경로가 `/health/healthcheck2.html`일 때 KCY-ec2-2-tg(EC2-2)로 연결

![image](https://user-images.githubusercontent.com/79209568/169734142-75b86b49-1caa-463e-9015-b8a32b11f451.png)

# 결과
- ALB의 DNS 주소 + **/health/healthcheck.html**  
  ![image](https://user-images.githubusercontent.com/79209568/169734405-8ab49ec4-0512-47eb-ab25-bf265fb0ea06.png)
- ALB의 DNS 주소 + **/health/healthcheck2.html**  
  ![image](https://user-images.githubusercontent.com/79209568/169734484-0ed564fe-9d81-4d20-b74c-6736db8cd727.png)


