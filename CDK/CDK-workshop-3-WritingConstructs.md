# CDK Workshop - Writing Connstructs
> [Workshop URL](https://cdkworkshop.com/)
> ### prev
> - [Workshop 1 - New Project](https://github.com/Clary0122/AWS/blob/main/CDK/CDK-workshop-1-NewProject.md)
> - [Workshop 2 - Hello CDK](https://github.com/Clary0122/AWS/blob/main/CDK/CDK-workshop-2-HelloCDK.md)

## 실습 목표
![image](https://user-images.githubusercontent.com/79209568/179674586-e44dbabe-0d93-4e01-8880-78b6c6f8f148.png)

- Hit Counter라는 새로운 구성을 정의한다.
- 이 구성은 API Gateway 백엔드로 사용되는 모든 Lambda 함수에 부착 가능하다.
- 각 URL 경로에 얼마나 많은 요청이 있었는지 계산해서 DynamoDB 테이블에 저장한다.

## Hit Counter API 정의
### 파일 생성
- lib 아래 `hitcounter.ts` 파일 생성
- 아래 내용 작성  
  ![image](https://user-images.githubusercontent.com/79209568/179676276-9372d605-d97d-4a7d-bd7e-945597815485.png)
  - constructor 인자는 scope, id, props 이고 cdk.Construct 기본 클래스로 전파한다.
  - props 인자는 `HitCounterProps`의 유형으로, "lambda.IFunction" 유형의 다운스트림 단일 속성을 포함한다. 이전 챕터에서 만든 람다 함수를 플러그인하여 hit count를 한다.
### Hit Count Handler
- `lambda/hitcounter.js` 파일 생성 후 아래 내용 작성
  ```js
  const { DynamoDB, Lambda } = require('aws-sdk');

  exports.handler = async function(event) {
      console.log("request:", JSON.stringify(event, undefined, 2));

      // create AWS SDK clients
      const dynamo = new DynamoDB();
      const lambda = new Lambda();

      // update dynamo entry for "path" with hits++
      await dynamo.updateItem({
          TableName: process.env.HITS_TABLE_NAME,
          Key: { path: { S: event.path } },
          UpdateExpression: 'ADD hits :incr' ,
          ExpressionAttributeValues: { ':incr': { N: '1' } }
      }).promise();

      // call downstream function and capture response
      const resp = await lambda.invoke({
          FunctionName: process.env.DOWNSTREAM_FUNCTION_NAME,
          Payload: JSON.stringify(event)
      }).promise();

      console.log('downstream response:', JSON.stringify(resp, undefined, 2));

      // return response back to upstream caller
      return JSON.parse(resp.Payload);
  };
  ```
- [HITS_TABLE_NAME]은 저장소에 사용할 DynamoDB 테이블의 이름
- [DOWNSTREAM_FUNCTION_NAME]은 다운스트림 AWS 람다 함수의 이름
- 테이블과 다운스트림 함수의 실제 이름은 앱을 배포할 때만 결정되기 때문에, 다음 단계에서 진행

### 리소스 정의
#### hit counter 구조에 리소스 추가
- `lib/hitcounter.ts`에 AWS Lambda 함수와 DynamoDB 테이블을 정의  
  
  ![image](https://user-images.githubusercontent.com/79209568/179692717-20db203e-5cba-4389-93bc-2afe96e909c8.png)

  - 경로를 파티션 키로 하는 DynamoDB 테이블을 정의
  - `lambda/hitcounter.handler` 코드에 바인딩된 lambda 함수를 정의
  - lambda의 환경변수를 자원의 DOWNSTREAM_FUNCTION_NAME 및 HITS_TABLE_NAME에 연결

### hit counter 사용
#### cdk 스택에 hit counter 추가
- `lib/cdk-workshop-stack.ts`에 코드 추가  
  ![image](https://user-images.githubusercontent.com/79209568/179706299-8c10b739-78d2-4c4d-8389-62d1b9e04e5f.png)
- API Gateway 핸들러를 `hello` 대신 `helloWithCounter.handler`로 변경함.
- 이는 기본적으로 엔드포인트가 히트할 때마다 API Gateway가 hit counter 핸들러로 요청을 라우팅하여 히트 카운터를 기록하고 hello 함수로 릴레이한다는 것을 의미한다.
- 그 후 응답은 역순으로 사용자에게 다시 전달 됨.

#### cdk deploy
```
cdk deploy
```
- 배포 완료 후 output에서 엔드포인트 확인  
  ![image](https://user-images.githubusercontent.com/79209568/179707846-f32edaf7-e190-44fb-9c09-e040a18d787d.png)

#### Test
- curl 또는 웹 브라우저를 사용해서 엔드포인트를 hit 해본다. (`-i`를 사용하여 HTTP 응답 필드 및 상태 코드를 표시해본다.)
  ```
  curl -i https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
  ```
- 오류 남  
  ![image](https://user-images.githubusercontent.com/79209568/179708509-bbe93df9-8e63-4134-aac0-436ede7b2fac.png)
