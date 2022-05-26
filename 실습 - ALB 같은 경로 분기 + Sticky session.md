# ALB 같은 경로 분기 - Sticky session

> ### \<INDEX>
> 1. [헬스체크 경로 통일](#헬스체크-경로-통일)
> 2. [리스너 규칙 편집](#리스너-규칙-편집)
> 3. [결과](#결과)
> 4. [Sticky Session이란?](#Sticky-Session이란)

> ### \<실습목표>
> - 두 아파치 서버의 헬스 체크 경로를 `/health/healthcheck.html`로 통일한다.
> - ALB DNS주소 + `/health/healthcheck.html`로 접속할 때 항상 같은 서버 말고 다른 아파치 서버 인스턴스로 갈 수 있도록 설정한다.
> - 그 후 Sticky Session 활성화해서 같은 인스턴스로만 가도록 설정해본다.
> - [현재까지 진행된 상황](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20ALB%20%EB%8C%80%EC%83%81%EA%B7%B8%EB%A3%B9%20%ED%8F%AC%ED%8A%B8%208088%EB%A1%9C%20%EB%B3%80%EA%B2%BD.md#alb-%EB%8C%80%EC%83%81%EA%B7%B8%EB%A3%B9-%ED%8F%AC%ED%8A%B8-8088%EB%A1%9C-%EB%B3%80%EA%B2%BD)
>   
>   ![image](https://user-images.githubusercontent.com/79209568/170389208-d62741c6-baec-4e0b-8dd6-d8e15b175bcb.png)


## 헬스체크 경로 통일
> #### EC2-2의 현재 헬스체크 경로가 `health/healthcheck2.html` 이므로 경로를 EC2-1과 같게 `health/healthcheck.html`로 변경해준다.

### EC2-2
- EC2-2 서버에 접속 후 이름 및 내용 변경(EC2-2의 html 임을 확인할 수 있도록)
  ```
  sudo mv /var/www/html/health/healthcheck2.html /var/www/html/health/healthcheck.html
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170163502-0866ad98-4c65-4374-860a-dd61a5e5e1c3.png)
- 대상그룹의 경로 수정  
  
  ![image](https://user-images.githubusercontent.com/79209568/170164733-511c3ed5-2605-4d73-a757-b220bd152d5d.png)

### EC2-1
- EC2-1의 healthcheck.html  
  
  ![image](https://user-images.githubusercontent.com/79209568/170163632-8d506188-3ad3-43d9-87bb-982ee10cc533.png)

## 리스너 규칙 편집
![image](https://user-images.githubusercontent.com/79209568/170389473-7ff3eacb-f47b-43f7-b5ce-15d77f0cc614.png)
- KCY-tg 에는 EC2-1, EC2-2 인스턴스가 등록 되어 있다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/170207809-79940724-1479-4aee-ba21-188197e17616.png)

## Sticky Session
> 라운드 로빈으로 작동하는 로드벨런서에 세션을 고정해서 사용자의 경험을 지속할수 있도록 도와주는 역할
> 다른 서버로 이동해도 사용자의 세션이 고정되어 있기 때문에 **새로고침할 때 마다 로그아웃** 되는 등의 문제를 피할 수 있다. 
### 기간 기반 쿠기, 애플리케이션 기반 쿠키
- 기간 기반 쿠키는 `AWSALB`로 모든 대상 그룹에서 사용되는 유일한 이름을 사용하고, 애플리케이션 기반 쿠키는 각 대상 그룹에 대해 쿠키 이름을 개별적으로 지정 가능하다.
- **기간 기반 고정**
  - 로드 밸런서 생성 쿠키(AWSALB)를 사용하여 대상 그룹의 동일한 대상으로 요청을 라우팅한다. 쿠키는 세션을 대상에 매핑하는 데 사용한다.
  - 애플리케이션에 자체 세션 쿠키가 없는 경우, 로드 밸런서가 사용자 요청을 동일한 대상으로 일관되게 라우팅 하는 고유한 고정 기간을 지정할 수 있다.
- **애플리케이션 기반 고정**
  - 클라이언트 대상 고정에 대한 사용자의 고유의 기준을 설정할 수 있는 유연성 제공
  - 애플리케이션 기반 고정을 활성화하면 로드 밸런서는 선택한 알고리즘을 기반으로 첫 번째 요청을 대상 그룹 내의 대상으로 라우팅한다. 
### Sticky Session 설정
![image](https://user-images.githubusercontent.com/79209568/170184291-879d9eac-8698-4f78-9542-b78a00cbab6f.png)
- 로드 밸런서 생성 쿠키로 2일 지속하도록 설정
> #### 오류
> - 리스너에서 그룹 Sticky 설정을 먼저 활성화하라는 오류가 뜸   
>   
>   ![image](https://user-images.githubusercontent.com/79209568/170188359-48698fc3-97fc-409c-853a-98a66082a63f.png)
> - 리스너의 그룹 수준 고정성을 활성화 시킴-1분  
>   ![image](https://user-images.githubusercontent.com/79209568/170188925-5bdf3d59-2fb9-4915-9214-d24c6b62d935.png)

### 결과
- AWSALB : 대상 그룹에서 설정한 Sticky 쿠키
- AWSALBTG : ALB 리스너에서 설정한 그룹 Sticky 쿠키

![image](https://user-images.githubusercontent.com/79209568/170189510-c404a667-dbea-43e5-aa2f-4ecfb83f5585.png)  
![image](https://user-images.githubusercontent.com/79209568/170191610-5b7ea70f-7577-43aa-b350-a7847b176005.png)



