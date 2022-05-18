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
- 👉 [**ALB 생성 및 속성 값 정리**](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20EC2%20%EB%B6%80%ED%95%98%ED%85%8C%EC%8A%A4%ED%8A%B8.md#ALB-%EC%83%9D%EC%84%B1)
# NLB
# CLB
