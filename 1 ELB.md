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
![image](https://user-images.githubusercontent.com/79209568/169176058-73ba62ee-2822-4fdc-8c75-79a51fe1ce4e.png)

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
- 👉 [**NLB 생성 및 속성 값 정리**]()

> ### 참고!!
> #### Source IP NAT 적용
> 헷갈리니 다시 찾아보기..

# CLB
![image](https://user-images.githubusercontent.com/79209568/169176028-77592d46-c8de-4118-a910-b1e43e7f4720.png)

- 가장 기본적인 형태이자 초기에 제공되던 서비스.
- 서버의 기본주소가 바뀌면 Load Balancer를 새로 생성해야히며 하나의 주소에 하나의 대상그룹으로 보내게 됨
- Layer 4계층에서 작동하기 때문에 데이터를 수정/변경할 수 없어 포트, 헤더 등의 변경을 하지 못함
- 이러한 구조는 서버의 구성과 아키텍처가 커지고 복잡해짐. -> Load Balancer의 필요 개수, 비용이 증가하게 됨.
- 👉 [**CLB 생성 및 속성 값 정리**]()

# 비교
|ALB/CLB|NLB|
|-|-|
|![image](https://user-images.githubusercontent.com/79209568/169176224-fecdbec8-eb64-4257-8849-31cd1f2fe981.png)|![image](https://user-images.githubusercontent.com/79209568/169176245-c25c4b08-c363-45b1-b42f-1e9d034b5a66.png)|
|Reverse Proxy 대로 클라이언트 IP와 서버 사이에 들어오고 나가는 트래픽이 모두 로드밸런서와 통신한다.|클라이언트 IP와 서버 사이에 서버로 들어오는 트래픽은 로드밸런서를 통하고 나가는 트래픽은 클라이언트 IP와 직접 통신한다.|
|보안그룹을 통한 보안이 가능|보안그룹 적용이 되지 않아서 서버에 적용된 보안그룹에서 보안이 가능|
|Client → 로드밸런서의 액세스 제한 가능|Client → 서버에서 액세스 제한 가능|
|IP주소가 변동되기 때문에 클라이언트에서 액세스 할 ELB의 DNS Name을 이용해야 함|할당한 Elastic IP를 Static IP로 사용 가능하여 DNS Name과 IP 주소 모두 사용이 가능|
|Name Server 또는 Route 53에서 CNAME을 사용해야 Domain Name 연동이 가능|Name Server 또는 Route 53에서 A레코드 사용이 가능|

<hr>

> ### 교차영역 로드밸런싱
> - 부하 분산이 불균형한 상태 (EC2에 할당된 요청의 비율이 불균형)  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/168936992-4dd79466-a02b-4032-9633-9f1e4eedd062.png)
>   - 이 경우 AZ a의 부하가 심하게 걸린다.
> - 불균형을 보완하기 위한 기능이 교차영역 로드밸런싱이다.  
>   
>   ![image](https://user-images.githubusercontent.com/79209568/168937086-603e8dc7-7b17-49db-a067-cdca7beaba07.png)

 
