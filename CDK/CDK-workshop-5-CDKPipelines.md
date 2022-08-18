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
- 여기서 CodeCommit은 자격증명을 요청한다. 한 번만 제공하면 된다. (안 물어봄..)

### 결과
- CodeCommit 콘솔에서 push 한 코드들이 있는 것을 확인할 수 있다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184648069-a6482bef-1967-43ee-8b07-859d7ddf1f26.png)

## Pipeline 생성
### 빈 pipeline 정의
- 파이프라인의 기본을 정의할 준비가 완료되었다.
- `lib/pipeline-stack.ts` 파일을 다음과 같이 편집한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184649753-0cb820fa-383c-40e6-abd6-cb77e879f369.png)
#### 구성 요소  
- `new CodePipeline()` : 파이프라인 초기화. 모든 파이프라인은 최소 다음의 필수 요소가 필요하다.
  - `synth()` : 파이프라인의 synthAction은 원본에서 종속성을 설치하고 CDK 애플리케이션을 빌드하고 동기화하는 데 필요한 명령을 설명한다. 이것은 항상 `synth` 명령으로 끝나야하며, npm 기반 프로젝트의 경우 항상 `npx cdk synth`여야 한다.
  - synth 단계의 입력은 CDK 소스 코드가 저장되는 저장소를 지정한다.

### pipeline 배포 및 결과 확인
- 파이프라인을 가동하려면 변경사항을 적용하고 마지막 CDK 구현을 실행하면 된다.
- CDK 파이프라인은 소스 repo의 각 커밋에 대해 자동 업데이트되므로 이 명령을 한 번만 실행한다.
  ```
  git commit -am "MESSAGE" && git push
  npx cdk deploy
  ```
- 배포가 완료되면 CodePipeline 콘솔로 이동하여 새 파이프라인을 확인한다.   
  
  ![image](https://user-images.githubusercontent.com/79209568/184652610-e643df39-68d7-4767-8ab1-17c165c246cf.png)

## pipeline에 애플리케이션 추가
### Stage 생성
- 현재 시점에서는 모든 커밋에 대해 자동으로 업데이트되는 CDK 파이프라인이 작동하는 것이 전부다.
- 애플리케이션을 배포할 파이프라인에 stage를 추가해야한다.
- `lib/pipeline-stage.ts` 파일을 생성한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184654282-ca086e41-8b74-432d-aa2a-393deb5b43c6.png)
  - 새로운 stage를 선언하고 그 단계에서 애플리케이션 스택을 인스턴스화 하는 것이다.
  - 해당 오류는 **애플리케이션 스택이 파이프라인에 의해 배포되도록 구성되지 않았기 때문**이다. `lib/cdk-workshop-stack.ts`를 다음과 같이 변경한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184655234-5adef702-3b8f-4b52-9b27-59bf6513f0c7.png)
  - 해당 스택의 scope 매개 변수가 구성 트리에서 app의 자식이어야 하는 cdk.App으로 정의 되어있었다.
  - 스택이 파이프라인에 의해 배포되고 있기 때문에 더 이상 app의 하위 항목이 아니므로 해당 유형을 Construct로 변경해야 한다.

### Pipeline에 stage 추가
- `lib/pipeline-stack.ts`에 다음 코드를 추가하여 파이프라인에 stage를 추가한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/184656409-ea5e9c24-abb2-44bd-a54c-3db2700bf4d4.png)
  - `WorkshopPipelineStage` 인스턴스를 가져오고 생성한다. 나중에 이 단계를 여러 번 인스턴스화할 수 있다. (예, 운영 배포 및 별도의 개발/테스트 배포)
  - 그 후 해당 stage를 파이프라인에 추가한다. (`pipeline.addStage(deploy)`)
  - CDK 파이프라인의 stage는 특정 환경에 함께 배포해야하는 하나 이상의 CDK 스택 집합을 나타낸다.

### Commit/Deploy
- 변경사항을 확정하고 레포에 푸시한다.
  ```
  git add .
  git commit -am "Add deploy stage to pipeline" && git push
  ```
- CodePipeline 콘솔에서 확인  
  
  ![screencapture-us-west-1-console-aws-amazon-codesuite-codepipeline-pipelines-WorkshopPipeline-view-2022-08-16-00_03_00](https://user-images.githubusercontent.com/79209568/184661440-50f6e42f-b0b0-4ee6-b769-8cdb4fe30011.png)

## Pipeline 다듬기
### 엔드포인트 가져오기
- 애플리케이션이 파이프라인으로 배포되고 있기 때문에 애플리케이션의 엔드포인트(TalbeViewer 및 API 게이트웨이)를 쉽게 찾을 방법이 없으므로 호출할 수 없다.
- 코드를 조금 더 명확하게 추가하도록 한다.
- `lib/cdk-workshop-stack.ts`를 다음과 같이 수정한다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/185305245-ebad6292-8c68-44f4-b067-f3ec301c2c3b.png)
  - `hcViewerUrl`과 `hcEndpoint` 출력 값을 추가하여 필요한 엔드포인트를 HitCounter 애플리케이션에 노출시킨다.
  - 코어 construct인 `CfnOutput`을 사용하여 Cloudformation 스택 출력으로 선언하고 있다.
- git push 진행
  ```
  git add .
  git commit -am "MESSAGE" && git push
  ```
- Cloudformaiton 스택 확인  
  
  ![image](https://user-images.githubusercontent.com/79209568/185309317-95aa0baf-5fc1-49b0-b96a-54d44ec2a961.png)
  - `CDKToolit` : 통합 CDK 스택.(부트스트랩 계정에서 항상 확인 가능) - 무시해도 됨
  - `WorkshopPipelineStack` : 생성했던 파이프라인을 선언하는 스택. - 당장 필요하지 않음
  - `Deploy-WebService` : **이 스택에 애플리케이션 존재**









