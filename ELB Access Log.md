# Access log 확인
- root 계정 변환
  `sudo su -`
- apache 프로세스의 리스트 확인
  `ps -ef | grep httpd`
  ![image](https://user-images.githubusercontent.com/79209568/169430140-3fbeeb10-0e0e-4fc4-b1fb-b601823ec610.png)
- root의 PID로 열린 모든 파일 목록과 정보를 확인한다. 그 중 httpd의 access log를 찾는다
  `lsof -p (root PID)`
  ![image](https://user-images.githubusercontent.com/79209568/169431067-e17c4ef9-926c-4cdf-bd63-1e747d587226.png)
  ![image](https://user-images.githubusercontent.com/79209568/169431075-fc0724a3-600d-41a3-8267-00a5393b958b.png)
- access log를 `-f`를 통해 계속 추적하도록 한다. 또 tail을 통해 끝에 5개만 나오도록한다(처음에)
  `tail -f /var/log/httpd/access_log` 
  ![image](https://user-images.githubusercontent.com/79209568/169431477-2280a607-46be-4b2b-bd75-81a82345e23b.png)

# 클라이언트 IP 확인
- CLB는 Layer4/Layer7, ALB는 Layer7에서 동작하기 때문에 HTTP 헤더에서 클라이언트 IP를 확인할 수 있다.
- NLB는 Layer4에서 동작해서 HTTP헤더를 이해하지 못하기 때문에 HTTP헤더의 XFF(X-Foarwarded-For)을 사용해서 클라이언트를 IP를 확인하지 못한다.
  - NLB는 따로 **클라이언트 IP 주소 보존 기능을 제공**
    > - 클라이언트 IP주소 보존 **활성화**  
    >   ![image](https://user-images.githubusercontent.com/79209568/169432203-565b7d7e-4dfa-4a75-8a14-1c8c0afaabb7.png)
    > - 클라이언트 IP주소 보존 **비활성화**  
    >   ![image](https://user-images.githubusercontent.com/79209568/169432294-15a3a9d1-0282-4d23-b986-f784aec0af85.png)

    >   

## CLB, ALB
> #### 아파치 설정 파일에서 LogFormat을 바꿔준다.
- `vi /etc/httpd/conf/httpd.conf`
- LogFormat에 `%{X-Forwarded-For}i`를 붙여준다.  
  ![image](https://user-images.githubusercontent.com/79209568/169432544-d4e23eb2-6a93-4001-b519-2dca22333505.png)
- 다시 로그 확인. 로드밸런서의 DNS주소를 새로고침 할 때 마다 클라이언트 IP를 확인할 수 있다.
  - NLB 로그  
    
    ![image](https://user-images.githubusercontent.com/79209568/169433482-667f01bd-ba60-4f14-8fde-4a7e99fe3cb3.png)
  - ALB 로그  
    
    ![image](https://user-images.githubusercontent.com/79209568/169433591-8f7d5542-7f87-415d-9ec7-d2a1202c9e0b.png)

## NLB
> #### 대상그룹 속성의 `클라이언트 IP 주소 보존`을 활성화
  ![image](https://user-images.githubusercontent.com/79209568/169431828-76974112-d31b-4ecd-b5af-4e03677bc63b.png)

#### 하지만!
- NLB의 대상 그룹이 **인스턴스** 일 경우에만 가능하다.
- NLB의 대상 그룹이 인스턴스의 IP일 경우(+ EIP) **타임아웃 오류**가 뜬다
