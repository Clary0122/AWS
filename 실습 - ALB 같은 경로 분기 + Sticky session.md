# ALB 같은 경로 분기 + Sticky session 실습

> ### \<INDEX>
> 1. [헬스체크 경로 통일](#헬스체크-경로-통일)
> 2. [리스너 규칙 편집](#리스너-규칙-편집)
> 3. [결과](#결과)

> ### \<실습목표>
> - 두 아파치 서버의 헬스 체크 경로를 `/health/healthcheck.html`로 통일한다.
> - ALB DNS주소 + `/health/healthcheck.html`로 접속할 때 항상 같은 서버 말고 다른 아파치 서버 인스턴스로 갈 수 있도록 설정한다.
> - 그 후 Sticky Session 활성화해서 같은 인스턴스로만 가도록 설정해본다.
> - [현재까지 진행된 상황](https://github.com/Clary0122/AWS/blob/main/%EC%8B%A4%EC%8A%B5%20-%20ALB%20%EB%8C%80%EC%83%81%EA%B7%B8%EB%A3%B9%20%ED%8F%AC%ED%8A%B8%208088%EB%A1%9C%20%EB%B3%80%EA%B2%BD.md#alb-%EB%8C%80%EC%83%81%EA%B7%B8%EB%A3%B9-%ED%8F%AC%ED%8A%B8-8088%EB%A1%9C-%EB%B3%80%EA%B2%BD)
>   
>   ![image](https://user-images.githubusercontent.com/79209568/170161753-5f31bfc4-f1b7-43b4-a6da-f1acff2104f7.png)

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
### 현재 ALB 리스너 규칙
![image](https://user-images.githubusercontent.com/79209568/170164170-0d4ebb00-59e9-4aa0-b085-fe5acd71a1a8.png)
- 경로 조건을 모두 지우고 요청 시 라우팅 경로를 각각 50%, 50%씩 나눠서 전달하도록 변경한다.
### 변경된 ALB 리스너 규칙
![image](https://user-images.githubusercontent.com/79209568/170165109-6dfc75fd-2fd0-4871-bfae-153a360d9fa1.png)

## 결과
- 같은 경로로 접속하면 로드밸런싱 되어 두 AZ의 인스턴스들로 50%의 확률로 분기되는 것을 확인할 수 있다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/170165599-1f7eaf4f-e92a-4dea-9433-3642efd973c5.png)  
  
  ![image](https://user-images.githubusercontent.com/79209568/170165667-e5706027-ecc6-4129-aec0-4b91b8391005.png)
