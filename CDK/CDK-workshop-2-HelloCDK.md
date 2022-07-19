# CDK Workshop - Hello, CDK
> [Workshop URL](https://cdkworkshop.com/)
> ### prev
> - [Workshop 1 - New Project](https://github.com/Clary0122/AWS/blob/main/CDK/CDK-workshop-1-NewProject.md)

## 실습 목표
![image](https://user-images.githubusercontent.com/79209568/175438339-b286fa5b-d984-4e44-a31d-a562281034b4.png)

- 이전 실습에서 만든 SNS/SQS 코드 대신 앞에 API 게이트웨이 엔드포인트가 있는 람다 기능을 추가한다.
- 사용자는 엔드포인트에 있는 모든 URL을 누를 수 있다.

## 샘플 코드 수정
- SQS/SNS를 사용하지 않을 것이기 때문에 `lib/cdk-workshop-stack.ts`에서 해당 내용을 지운다.  
  ![image](https://user-images.githubusercontent.com/79209568/175447580-4934a802-897c-4b7f-98f6-5d482831ee64.png)

### CDK Diff
- 현재 CDK 앱과 최근 배포와의 차이를 보여준다.
- `cdk deploy` 하기 전에 어떤 변화가 생길지 미리 한 번 보는 것이 좋다.
  ```
  cdk diff
  ```
  ![image](https://user-images.githubusercontent.com/79209568/175466814-04bc189c-dfe4-493f-8eff-29c9eb7979ed.png)

### 수정 코드 배포
- 배포 후 리소스들이 삭제 된 것을 확인할 수 있다.
  ```
  cdk deploy
  ```
  
## 람다 핸들러 코드
- `lambda` 폴더를 프로젝트의 루트에 생성한다.   
  
  ![image](https://user-images.githubusercontent.com/79209568/175468261-272a10f6-a90d-4dab-8a70-66222afc1ed7.png)

- `.gitignore` 파일에 `!lambda/*.js`를 추가한다. cdk init으로 생성된 프로젝트는 기본적으로 .js 파일을 무시하기 때문에 git을 사용하여 이 파일을 추적하려면 추가해야 한다. 이렇게 하면 나중에 pipeline에서 람다 assets을 검색할 수 있다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/175468423-9b455494-0d25-48f0-8cba-8ac7611d9e78.png)
- `lambda` 폴더에 `hello.js` 파일 생성 후 해당 내용을 작성한다.
- 해당 내용은 `Hello, CDK! You've hit [url path]`라는 텍스트를 반환하는 간단한 람다 함수다.
- 함수의 출럭에는 HTTP 상태 코드와 HTTP 헤더도 포함된다. 
- API Gateway 에서 사용자에 대한 HTTP 응답을 공식화하는 데 사용된다.
  
  ![image](https://user-images.githubusercontent.com/79209568/175468732-6f0c2316-54ea-44ff-a6e0-e3ab3b1504c7.png)

### 람다 함수를 스택에 추가
- `lib/cdk-workshop-stack.ts`에 AWS 람다 리소스를 정의해준다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/175470319-557c4504-92fc-4617-8b7c-2f3c50043caf.png)
  - NodeJS 런타임을 사용
  - 핸들러 코드는 "lambda" 디렉터리에서 로드
  - 핸들러 함수의 이름이 "hello.handler("hello"는 파일 이름, "handler"는 export한 함수 이름)

> ### Construcs(구성요소)와 Constructors(생성자)
> - `CdkWorkshopStack`와 `lambda.Function` 및 CDK의 다른 많은 클래스의 Constructors(생성자)는 서명(scope, id, props)을 가지고 있다. 왜냐하면 이 **많은 클래스가 모두 Construcs(구성요소)이기 떄문이다.**
> - Constructs는 CDK앱의 기본 구성요소이다.
> - Constructs는 scope를 통해 더 높은 수준의 추상화로 함께 구성될 수 있는 추상적인 클라우드 구성요소를 나타낸다.
> - Constructs는 항상 다른 construcs의 scope내에서 생성되며 scope 내에서는 고유 해야하는 Constructor(식별자)를 가져야한다.
> - 따라서 Constructors는 항상 다음의 서명을 가진다.
>   - `scope` : 항상 이 구성이 생성되는 범위. 
>   - `id` : 구성의 로컬 아이덴티티. 동일한 범위 내에 구성 중 고유해야 하는 ID. CDK는 이 ID를 사용하여 이 범위에 정의된 각 리소스에 대한 CF Logical ID를 계산한다.
>   - `props` : (선택적) 초기화 속성 집합.각 구조에 따라 다르다. 예를들어 `lambda.Function`의 구성은 runtime, code, handler이다.

### Deploy 진행
- deploy 전에 diff로 바뀐 점을 확인 해보면,  
  
  ![image](https://user-images.githubusercontent.com/79209568/175480051-f38ebbfb-d84e-408c-b730-5a71db097588.png)
  - "AWS::Lambda::Function" 리소스를 합성
  - 핸들러 코드의 위치를 전파하기 위해 툴킷에서 사용하는 몇 가지 클라우드 포메이션 매개 변수를 합성
- deploy 진행
  ```
  cdk deploy
  ```
### 람다 함수 테스트
- 람다 함수 콘솔에서 CdkWorkshopStack-HelloHandler~ 함수 클릭 → Test 버튼 → Configure test event 클릭  
  
  ![image](https://user-images.githubusercontent.com/79209568/175484681-797f33a5-2e40-48c8-ba91-009046a65130.png)
- 테스트 이벤트 구성
  - 이름 : test
  - 템플릿 : API Gateway AWS Proxy  
  
  ![image](https://user-images.githubusercontent.com/79209568/175486679-5de0068a-215b-4e3f-89b7-313a94ad5f17.png)
- Test 클릭 후 실행 완료될 때까지 기다린다. 그 후 Execution result의 Response 확인  
  
  ![image](https://user-images.githubusercontent.com/79209568/175487030-8b3cc4bf-ee99-4543-87be-47374d7a92e1.png)

## CDK Watch
> `cdk deploy --hotswap`과 `cdk watch`를 통해 개인 구축 시간을 단축할 수 있는 방법
- 위의 실습까지는 스택을 업데이트할 수 있는 유일한 도구가 `cdk deploy`이다.
- 그러나 `cdk deploy`는 CloudFormation 스택을 배포하고 디스크에서 bootstrap 버킷으로 lambda 디렉토리를 업로드해야 하므로 시간이 걸린다.
- 람다 코드만 변경한다면 CloudFormation 스택을 업데이트할 필요 없음

### Full deployment
- 전체 배포를 수행해서 시간이 얼마나 걸리는 지 확인해본다.  
  
  ![image](https://user-images.githubusercontent.com/79209568/175499100-3558cc6c-ff95-4c43-a533-02d451cc892e.png)
  - 총 배포 시간 32.57초  
  
  ![image](https://user-images.githubusercontent.com/79209568/175499302-1c285cb5-e1c3-4051-b26d-7b3851a58506.png)

### Hotswap deployment
- 배포 속도를 높이기 위해 의도적으로 CloudFormation 스택에 드리프트를 도입하는 것이므로 개발 목적으로만 사용하는 것이 좋다. 프로덕션 배포에 핫스왑을 사용하면 안된다!
- 핫스왑을 이용한 배포를 수행해서 시간이 얼마나 걸리는 지 확인해본다.
  
  ![image](https://user-images.githubusercontent.com/79209568/175499820-9c7ef1e9-c890-40ab-b7fa-23144b8162a3.png)

  ```
  cdk deploy --hotswap
  ```
  - 총 배포 시간 2.55초  
  
  ![image](https://user-images.githubusercontent.com/79209568/175500111-5b81a5e7-54af-4510-9e7c-f7daa59258b6.png)
  
  - 코드가 직접 바꼈는지 람다 콘솔에서 확인 → 바뀜!
  
  ![image](https://user-images.githubusercontent.com/79209568/175500546-982a59fb-3f20-4a43-a6f5-1ec31d6786fb.png)

### cdk watch
- cdk watch는 `cdk deploy`, `cdk deploy --hotswap`과는 달리 코드와 asset을 모니터링하여 변경 사항이 감지되면 자동으로 배포를 수행한다.
- 기본적으로 `--hotswap` 플래그를 사용한다. `--hotswap` 플래그는 변경 사항을 검사하고 이러한 변경 사항을 핫 스왑할 수 있는지 여부를 결정한다.
- `cdk watch --no-hotswap`으로 핫스왑 동작을 비활성화한다.
- cdk watch 명령이 실행될 때 cdk.json 파일의 "watch" 설정에 따른다.
  - "include"와 "exclude" 두 개의 하위 키가 있고, 각 하위 키는 단일 문자열이거나 문자열 배열일 수 있다.
  - 각 항목은 cdk.json 파일의 위치와 관련된 경로로 해석된다.
  
  ![image](https://user-images.githubusercontent.com/79209568/179669306-6d78651a-e0bc-4da4-9541-02b2b3b6db31.png)

- 현재 샘플 앱에는 "watch" 설정이 포함되어 있다. 'hello.js' 파일을 관찰하고 이싶으니 "exclude" 목록에서 `**/*.js`를 제외시킨다.
  ![image](https://user-images.githubusercontent.com/79209568/179669566-dc4716f8-12a5-4233-9c2d-26a9cb50c4e1.png)

#### cdk watch 실행
```
cdk watch
```
- watch를 부르면 초기 배포가 트리거되고 즉시 cdk.json에서 지정한 파일을 관찰하기 시작한다.
  ![image](https://user-images.githubusercontent.com/79209568/179669844-143903f6-9d31-4865-9c9e-330271f3abf5.png)

#### hello.js 파일 내용 변경
- 파일 내용 변경 후 저장  
  ![image](https://user-images.githubusercontent.com/79209568/179670002-fdb178ed-89f0-4851-a9f1-b31565ce84ab.png)

- 변경 사항을 저장하면 cdk watch는 파일이 변경되었음을 인식하고 새 배포를 트리거 한다.
  ![image](https://user-images.githubusercontent.com/79209568/179670224-5814a867-b476-45a5-9e0f-d6a8d1fdbc85.png)
- 핫 스왑으로 CloudFormation 배포를 무시하고 대신 Lambda 서비스에 직접 배포한다.

### API Gateway
- 이번에는 기능 앞에 API 게이트웨이를 추가한다.
- API Gateway는 인터넷에 있는 HTTP 클라이언트로 칠 수 있는 공개 HTTP 엔드포인트를 노출한다.
- API 루트에 마운트 된 람다 프록시 통합을 사용한다. 즉 URL 경로에 대한 모든 요청이 람다 함수에 직접 프록시되고 함수의 응답이 사용자에게 다시 반환된다.


#### LambdaRestApi 추가
- `lib/cdk-workshop-stack.ts` 파일에 API의 엔드포인틀르 정의하고 Lambda 함수와 연결한다.
  ![image](https://user-images.githubusercontent.com/79209568/179671714-077999e7-5833-4d9a-a948-795c88e9dfa0.png)

#### cdk diff
```
cdk diff
```
- 리소스 12개 추가 됨  
  
  ![image](https://user-images.githubusercontent.com/79209568/179672137-cd2ea3e4-13ba-4f2d-bcde-f8c0d004b4cd.png)

#### cdk deploy
```
cdk deploy
```
- 배포가 완료되면 API Gateway 구문에 의해 자동으로 출력되는 API Gateway 엔드포인트의 URL을 포함한 스택 출력이 보인다.  
  ![image](https://user-images.githubusercontent.com/79209568/179673137-ce8c8b59-2b02-4a07-8c81-80a90be3701d.png)

#### 앱 테스트
- `curl`을 통해 엔드포인트를 hit 테스트 해본다.

  ```
  curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
  ```
  ![image](https://user-images.githubusercontent.com/79209568/179673471-db1e31a5-6e8b-4af9-b283-bd8c0fc53f22.png)

- 웹브라우저에서도 확인 가능  
  
  ![image](https://user-images.githubusercontent.com/79209568/179673605-175020ba-3908-4315-99e8-4e73818b5e79.png)

