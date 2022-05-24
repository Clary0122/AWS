# ELB Access Log
## ALB
- ALB 속성에서 액세스 로그를 활성화
- 로그를 저장할 S3 버킷 명을 입력하고 `내 대신 이 위치를 생성`을 체크한 후 저장하면 해당 ALB와 같은 리전에 S3 버킷을 생성한다.
  
  ![image](https://user-images.githubusercontent.com/79209568/170073877-ca06127d-d8f3-43ca-b8e5-5fb10cb94997.png)

- 생성된 버킷의 정책  
  
  ![image](https://user-images.githubusercontent.com/79209568/170075866-07805f38-db97-41d9-861c-500fbf87e8f5.png)

  > #### elb-account-id
  > ![image](https://user-images.githubusercontent.com/79209568/170076776-ee7646e2-20f8-4b81-853c-5ff253f58b1d.png)

## CLB
- CLB 속성에서 액세스 로그를 활성화
  
  ![image](https://user-images.githubusercontent.com/79209568/170070914-b5c542ea-6ae1-4123-a952-b76843f93809.png)

## 로그 확인
![image](https://user-images.githubusercontent.com/79209568/170077315-8e5691f1-253a-486a-b59f-00d195e2984f.png)

## NLB는?
- NLB 로그에는 TCP 트래픽이 아닌 **TLS 트래픽이 있는 로그**만 포함된다
- TCP 모드에서 NLB에 대한 로그를 캡처하기 위해 NLB에 연결된 네트워크 인터페이스에 대한 흐름 로그를 활성화한 다음 흐름 로그를 S3 또는 CloudWatch에 저장할 수 있다.  
- VPC 흐름 로그를 확인해야 합니다.
  
  ![image](https://user-images.githubusercontent.com/79209568/170073459-098ef277-438f-4d28-a915-9e24970b5fac.png)
