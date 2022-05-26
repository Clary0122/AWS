# ALB 8088포트 80포트로 리다이렉팅
> ### \[INDEX]
> 1. [ALB 리스너 설정](#ALB-리스너-설정)
> 2. [대상그룹, 보안그룹 설정](#대상그룹-보안그룹-설정)
> 3. [결과](#결과)

> ### \[실습목표]
> ![image](https://user-images.githubusercontent.com/79209568/170391900-9b1e590e-9592-40bc-8684-8e4b6cdfe23c.png)

## ALB 리스너 설정
- 80번 포트로 들어오면 8088로 리다이렉션  
  
  ![image](https://user-images.githubusercontent.com/79209568/170392304-ccd0a716-ef6f-4ab9-83b7-a546b96aceec.png)
- 다시 ALB로 8088포트로 들어오기 때문에 8088 리스너도 추가 해준다. 8088로 들어올 땐 80번 포트로 가는 대상그룹으로   
  
  ![image](https://user-images.githubusercontent.com/79209568/170392451-3d3d1f98-dc99-4bd4-b371-9ab701a60592.png)

## 대상그룹, 보안그룹 설정
### ALB
#### 대상그룹
- 프로토콜:포트가 HTTP\:80, 등록된 대상 인스턴스도 80포트
  
  ![image](https://user-images.githubusercontent.com/79209568/170392773-40971e5b-1b7a-42ed-b0f6-5a8b38ae89d4.png)
#### 보안그룹
- 80으로 들어온 후 리다이렉션으로 8088로 들어오기 때문에 인바운드 규칙을 추가해준다.
  
  ![image](https://user-images.githubusercontent.com/79209568/170392950-912489c7-ebe9-43d3-b814-f8ae3b17f6cc.png)
### EC2
#### 보안그룹
- 80포트로 들어오게 해준다.

## 결과
- 리다이렉션되어 URL에 8088포트
![image](https://user-images.githubusercontent.com/79209568/170393164-c73086a0-05e2-4804-9759-e381d074b2ac.png)
