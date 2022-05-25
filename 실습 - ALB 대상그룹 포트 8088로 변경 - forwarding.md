# ALB 대상그룹 포트 8088로 변경
> \<**INDEX**>
> 1. [아파치 서버 리스너 변경](#EC2-아파치-서버-리스너-변경)
> 2. [대상 그룹. 보안 그룹 설정](#대상-그룹-보안-그룹-설정)
> 3. [ALB 리스너 설정](#ALB-리스너-설정)
> 4. [결과](#결과)

> \<**실습목표**>
> ![image](https://user-images.githubusercontent.com/79209568/169752755-5315538b-81e0-422c-969e-4eceeef16ea9.png)
> - ALB는 80포트로 보내고 분기 받는 AZ-a의 EC2 인스턴스는 8088 포트로 받게한다.
> - 아파치 서버의 httpd.conf 파일을 편집하는 것이 키포인트

# EC2 아파치 서버 리스너 변경
## httpd.conf
- EC2 아파치 서버에 접속
- httpd.conf 파일 편집기
  `sudo vi /etc/httpd/conf/httpd.conf`
- `Listen 80` → `Listen 8088`  
  
  ![image](https://user-images.githubusercontent.com/79209568/169753454-e9598e9b-72c7-472e-94aa-5b62e9c61911.png)
- 아파치 재 실행
  `sudo systemctl restart httpd`

# 대상 그룹. 보안 그룹 설정
## ALB 대상그룹 생성
- HTTP 8088포트 생성  
  
  ![image](https://user-images.githubusercontent.com/79209568/169754442-9d0d2fdc-fc84-4e89-aaa1-b289b96b4df3.png)
  ![image](https://user-images.githubusercontent.com/79209568/169754494-f13d0fd5-a642-40d7-8650-83947e2c1c98.png)
## EC2 보안그룹 포트 변경
- EC2 보안그룹 포트를 8088로 변경  
  
  ![image](https://user-images.githubusercontent.com/79209568/169754873-5921801a-2f90-402f-8e88-8982f0c0253a.png)

# ALB 리스너 설정
- 80포트 리스너를 8088 타겟그룹으로 가도록 변경
![image](https://user-images.githubusercontent.com/79209568/169755178-dcc79077-9391-400b-bf44-654f2e1285bf.png)

# 결과
![image](https://user-images.githubusercontent.com/79209568/169756985-4cbb5e81-2f8c-4f2e-8bc0-b60277418e05.png)

> ### 오류
> - 현재 기본페이지로 갔을 때는 잘 가지만 상태체크 주소인 /health/healthcheck.html로 가면 504 Gateway Time-out 오류가 나는 상황
> - /health/healthcheck.html 경로가 80포트인 KCY-tg 로 가는 리스너가 우선순위가 높기 때문에 난 오류라고 예상 됨
>   ![image](https://user-images.githubusercontent.com/79209568/169751272-eac31bc3-27bf-4fbe-879a-07000c26c743.png)
> ### 해결 시도
> #### 1. 상위 우선순위 리스너 타겟그룹을 8088로 변경 - 해결 됨
> ![image](https://user-images.githubusercontent.com/79209568/169758338-a4b822f6-113a-43e1-8034-0d7ebc4aa32e.png)
>   
> #### 2. 리스너 삭제 - 해결 됨
> ![image](https://user-images.githubusercontent.com/79209568/169759179-f10c8975-6f14-478a-8095-ef88d97bcecf.png)
> #### 두 방법 다 해결 됨
