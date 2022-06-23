# CDK Workshop
> [workshop url](https://cdkworkshop.com/)

## CDK
- Cloud Development Kit
- CDK는 크게 **행위, 정의, 호출**의 역할을 하고 가장 중요한 개념이니 기억해두기
- 리소스 정의할 때는 리소스 생성 순서에 주의해야 한다.
- 리소스의 생성은 지원하지만 실행은 지원하지 않는다.
- 지원을 하지 않는 것도 있으니 API를 확인해서 직접 구현하도록 한다.
- CodePipeline을 통해 플로우를 생성한다.
- 테라폼보다 CDK를 선호하는 이유는 로직 구현과 수정이 간편하기 떄문이다.
- CDK용 IDE를 Cloud9으로 할 경우 인스턴스는 최소 t2.small 이상은 되어야 한다.
- 처음 배포할 때는 CodePipeline의 SelfMutate 스테이지를 꺼준 후 배포해야 오류가 나지 않는다.
- 크게 세 가지 주요 명령어
  - `synth` : 클라우드 포메이션의 스택을 생성하는 명령어
  - `diff` : 지금 배포되어 있는 것과 배포할 것에 다른 점이 있는지 확인하는 명령어
  - `deploy` : 앱 자체를 실제 클라우드 포메이션 스택으로 만들어서 배포하는 명령어
    - 이 명령어는 synth와 diff를 같이 해 주고 배포를 실행

## CDK 프로젝트 생성
- typescript 언어로 프로젝트 생성
  ```
  cdk init sample-app --language typescript
  ```
## 프로젝트 구조
![image](https://user-images.githubusercontent.com/79209568/175210072-e31c6d68-86cb-4800-994a-01cb48f2af2f.png)

- `bin/cdk-workshop-stack.ts` : CDK 애플리케이션의 메인 스택이 정의 되는 곳
- `bin/cdk-workshop.ts` : CKD 응용 프로그램의 진입점. ckd-workshop-stack.ts 파일에 정의된 스택을 로드
- `package.json` : npm 모듈 매니페스트. 여기에는 앱 이름, 버전, 종속성 및 "watch" 및 "build"와 같은 빌드 스크립트 정보가 포함 됨. 
- `cdk.json` : 툴킷에 앱을 실행하는 방법
- `tsconfig.json` : 해당 프로젝트의 typescript 구성
- `node_modules` : npm에서 유지 관리하며 프로젝트의 모든 종속성을 포함
