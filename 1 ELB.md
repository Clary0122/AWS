> \<INDEX>
> - [Load Balancer](#Load-Balancer란?)
> - [ALB](#ALB)
> - [NLB](#NLB)
> - [CLB](#CLB)

# Load Balancer 란?
- AWS의 VPC에 탑재되며 사용자의 요청을 받고 이를 VPC내의 리소스(EC2등)에 적절히 부하 분산한다.
- 외부의 요청을 받아들이는 **리스너**, 요청을 분산/전달할 리소스의 집합인 **대상그룹**으로 구성된다.
  - 리스너
    - 외부의 요청을 받아들이기 때문에 **서비스 포트**를 보유한다.
    - 외부의 요청이 서비스 포트로 접속하는 요청만을 처리한다.
  - 대상그룹
    - **서비스 포트**를 보유한다.
    - 부하 분산 대상인 리소스(EC2 등)은 해당 포트가 열려 있어야 한다.
- **헬스체크**를 통해 부하분산 대상인 대상그룹 내 리소스들을 끊임없이 상태 체크한다.
- 크게 **Internet LB**, **Internal LB**로 구분된다.
  - Internet LB : 외부 인터넷에 접속이 가능한 **공인IP와 사설IP** 모두를 가지고 있음
  - Internal LB : 내부에서의 접근만을 허용하며 **사설IP**만을 가지고 있음
- 유형 : **ALB**(Application Load Balancer), **NLB**(Network Load Balancer), **GLB**(Gateway Load Balancer), **CLB**(Classic Load Balancer)

# ALB
- 7계층에 해당하는 Application Layer의 특성을 이용하는 로드밸런서
- 그 중 HTTP, HTTPS의 특성을 주로 다루는 로드밸런서이다. -> 단순 부하분산뿐만 아니라 **HTTP 헤더 정보를 이용해 부하분산**을 실시한다.
  - HTTP 헤더 값을 보고 이 요청은 A 대상그룹, 저 요청은 B 대상그룹!
- 기본적으로 [교차영역 로드밸런싱](#교차영역-로드밸런싱)(Cross Zone Load Balancing)이 **활성화** 되어있다.
- 👉 [**ALB 생성 및 속성 값 정리**](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20EC2%20%EB%B6%80%ED%95%98%ED%85%8C%EC%8A%A4%ED%8A%B8.md#ALB-%EC%83%9D%EC%84%B1)

# NLB
- 7계층에 해당하는 Transport Layer의 특성을 이용하는 로드밸런서
- TCP, UDP를 사용하는 요청을 받아들여 부하분산을 실시하는 로드밸런서이다.
  - 송신자와 수신자간의 데이터 송수신을 위한 커넥션이 필요한데 커넥션은 네트워크 상의 기다란 줄로 존재하는 것이 아닌 논리적인 존재를 의미한다.
  - 송수신자가 모두 알고있기 때문에 네트워크 상 커넥션이 존재하는 것처럼 표현함.
  - 그리고 Transprot Layer에 소속된 프로토콜 TCP와 UDP는 이 커넥션을 생성하기 위한 역할
- 하지만 **TCP와 UDP의 상위 프로토콜을 이해하지 못한다.**
  - TCP의 상위 프로토콜 : HTTP, HTTPS, FTP, DNS
  - UDP의 상위 프로토콜 : DNS, SNMP, DHCP
- **ALB**는 Layer4의 TCP뿐만 아니라 Layer 7의 HTTP, HTTPS, WebSocket을 이해하고 처리할 수 있기 때문에 **많은 리소스를 소모해야한다**. 하지만 **NLB**는 Layer 4너머의 프로토콜을 이해할 필요없이 TCP, UDP만을 이해하면 되기 때문에 **ALB보다 리소스 소모고 적다**. -> **많은 트래픽 처리가 가능!**
- 기본적으로 교차영역 로드밸런싱이 **비활성화** 되어있다.

> ### 참고!!
> #### Source IP NAT 적용
Source IP NAT 
# CLB


<hr>

> ### 교차영역 로드밸런싱
> - 부하 분산이 불균형한 상태 (EC2에 할당된 요청의 비율이 불균형)  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/168936992-4dd79466-a02b-4032-9633-9f1e4eedd062.png)
>   - 이 경우 AZ a의 부하가 심하게 걸린다.
> - 불균형을 보완하기 위한 기능이 교차영역 로드밸런싱이다.  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/168937086-603e8dc7-7b17-49db-a067-cdca7beaba07.png)

 
