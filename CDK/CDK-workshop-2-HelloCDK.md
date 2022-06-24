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
- 현재 CDK 앱과 최근 33333
