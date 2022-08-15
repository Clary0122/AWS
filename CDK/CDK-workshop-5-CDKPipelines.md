# CDK Workshop - CDK Pipelines
> [Workshop URL](https://cdkworkshop.com/20-typescript/70-advanced-topics/200-pipelines.html)


## 실습 목표
- 이전 장에서 개발한 앱에 대한 CD(Continuous Deployment) 파이프라인을 만든다.
- CD는 대부분의 웹 프로젝트에서 중요한 구성요소이지만 설정하기 어려울 수 있다. CDK 파이프라인 구조를 사용하면 기존 CDK 인프라 설계 내에것 프로세를 쉽고 간편하게 수행 가능하다.

## Pipeline 시작
### Create Pipeline Stack
- 먼저, 우리의 파이프라인을 포함할 스택을 만든다. 
- 실제 "Productions" 애플리케이션과 별개이기 때문에 완전히 자급자족하도록 만든다.
- `lib/pipeline-stack.ts` 파일을 생성한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184638082-e47a02fd-ab9a-4f9e-97c5-962338e750d3.png)

### CDK 배포 진입점 업데이트
- 우리의 파이프라인의 목적은 우리의 애플리케이션 스택을 배포하는 것이기 때문에, 더 이상 메인 CDK 애플리케이션이 우리의 원래 앱을 배포하는 것을 막아햐 한다.
- 진입점을 변경하여 파이프라인을 배포하면 애플리케이션이 배포된다.
- `bin/cdk-workshop.ts`의 코드를 다음과 같이 수정한다.  

  ![image](https://user-images.githubusercontent.com/79209568/184638944-03cbb53a-cb84-45a5-8a2f-2dfa0eb12620.png)

## Repository 생성
### Pipeline Stack에 repo 생성
- CD 파이프라인의 첫 번째는 소스 제어다. 프로젝트 코드를 포함하는 CodeCommit 저장소를 생성한다.
- lib/pipeline-stack.ts 파일을 다음과 같이 수정한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184639913-857bd96f-f31d-445f-85b6-dbc847f27bb6.png)

## deploy
```
npx cdk deploy
```

## Repo 정보 가져오기 및 커밋
- Repo를 사용하기 전에 우리의 코드를 입력해야한다.
### Git 인증 정보
- 코드 입력 전에 리포에 대한 Git 자격 증명이 필요하다.
- IAM 콘솔로 이동 후 다음 Users(사용자)로 이동한 다음 원하는 사용자로 이동. `보안 자격 증명` 클릭  
  
  ![image](https://user-images.githubusercontent.com/79209568/184644601-3b969498-f1d5-42df-8af4-53568cf7b3fc.png)
- `AWS CodeCommit에 대한 HTTPS Git 자격 증명` 에서 `자격 증명 생성` - 다운로드  
  
  ![image](https://user-images.githubusercontent.com/79209568/184644811-8a066c08-0fc1-455a-a666-bb426d00e170.png)

### Git 리모트 추가
- CodeCommit 콘솔로 이동하여 `URL 복제` 열에서 "HTTPS"를 클릭한다. 해당 링크를 복사하여 로컬에 레포를 추가할 수 있다.  
  ![image](https://user-images.githubusercontent.com/79209568/184645335-6a36d8f8-210b-4f14-bc6e-7300b28c4213.png)

- 기본적으로 CDK `.gitignore` 파일에는 모든 *.js 파일이 일반적으로 npm-ts에 의해 생성되므로 무시하기 위한 참조가 포함되어 있다.
- js로 쓰여진 람다 파일을 가지고 있기 때문에, 그것들을 무시해서는 안됨. `.gitignore` 파일에 `!lambda/*.js`를 추가한다. 그러면 git이 lambda/ 디렉토리에 모든 *.js 파일을 포함하도록 지시한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184646684-7c875cf4-a110-444d-9358-74c19cc1c9f4.png)

- 코드를 CodeCommit에 푸시하기 전에 변경 사항을 로컬 Git에 커밋해야한다.  
  ```
  git add -A && git commit -m "Updated .gitignore and etc"
  ```
- git config에 원격 레포를 추가 한다. 뒤의 URL은 콘솔에서 복사한 https 주소다.
  ```
  git remote add origin https://git-codecommit.~WorkshopRepo
  ```
- 레포에 코드를 푸시. (--setupstream은 Git에게 레포의 현재 빈 마스터 브랜치를 오버라이드하도록 함)
  ```
  git push --set-upstream origin master
  ```
- 여기서 CodeCommit은 자격증명을 요청한다. 한 번만 제공하면 된다.

### 결과
- CodeCommit 콘솔에서 push 한 코드들이 있는 것을 확인할 수 있다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184648069-a6482bef-1967-43ee-8b07-859d7ddf1f26.png)



