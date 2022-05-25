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

## 로그 속성
|로그|포맷|설명|
|:--|:--:|:--|
|http|type|요청 또는 연결의 유형<br>- http : HTTP<br>- https : TLS를 통한 HTTP<br>- h2 : TLS를 통한 HTTP/2<br>- grpcs : TLS를 통한 gRPC<br>- ws : WebSockets<br>- wss : TLS를 통한 WebSockets|
|2022-05-24T15:14:27.262920Z|time|- 로드 밸런서가 클라이언트에 응답을 생성한 시간(ISO 8601 형식). <br>- WebSockets의 경우, 연결이 종료된 시점|
|app/KCY-alb/51f2c9ef6f278168|elb|- 로드 밸런서의 리소스 ID|
|95.214.235.205:42698|client:port|요청을 하는 클라이언트의 IP 주소 및 포트|
|10.0.6.199:8088|target:port|- 이 요청을 처리한 대상의 IP 주소 및 포트<br>- 클라이언트가 전체 요청을 전송하지 않은 경우에는 로드 밸런서가 대상으로 요청을 디스패치 할 수 없고 이 값은 -로 설정<br>- 대상이 Lambda 함수인 경우 이 값은 -로 설정<br>- AWS WAF에서 요청을 차단한 경우, 이 값은 -로 설정되고 elb_status_code 값은 403으로 설정|
|0.000|request_processing_time|로드 밸런서가 요청을 수신한 시간부터 대상으로 요청을 전송한 시간까지의 총 경과 시간(초, 밀리초 단위)|
|0.001|target_processing_time|로드 밸런서가 대상에 요청을 보낸 시간부터 대상이 응답 헤더를 보내기 시작할 때까지의 총 경과 시간(초, 밀리초 단위)|
|0.000|response_processing_time|- 로드 밸런서가 대상에서 응답 헤더를 수신한 시간부터 클라이언트에 응답을 보내기 시작할 때까지의 총 경과 시간(초, 밀리초 단위)<br>- 로드 밸런서의 대기 시간과 로드 밸런서에서 클라이언트까지의 연결 확보 시간|
|404|elb_status_code|로드 밸런서의 응답 상태 코드|
|404|target_status_code|- 대상의 응답 상태 코드<br>- 대상으로 연결이 설정되고 대상이 응답을 전송한 경우에만 이 값이 기록(그렇지 않으면 -)|
|230|received_bytes|클라이언트로부터 수신된 요청의 크기(바이트)|
|375|sent_bytes|클라이언트에게 보낸 응답의 크기(바이트)|
|"GET http://3.114.196.86:80/.env HTTP/1.1"|"request"|- HTTP 메서드 + protocol://host:port/uri + HTTP 버전 형식<br>- 로드 밸런서는 요청 URI를 기록할 때 클라이언트가 보낸 URL을 원본 그대로 보관|
|"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36"|"user_agent"|- 요청을 보낸 클라이언트를 식별하는 사용자 에이전트 문자열<br>- 하나 이상의 제품 식별자, 제품\[/버전]으로 이루어져 있음|
|-|ssl_cipher|- \[HTTPS 리스너] SSL 암호<br>- 리스너가 HTTPS 리스너가 아닌 경우 이 값은 -로 설정|
|-|ssl_protocol|- \[HTTPS 리스너] SSL 프로토콜<br>- 리스너가 HTTPS 리스너가 아닌 경우 이 값은 -로 설정|
|arn:aws:elasticloadbalancing:ap-northeast-1:090274807648:targetgroup/KCY-tg-8088/3f696b950acec560|target_group_arn|대상 그룹의 ARN(Amazon 리소스 이름|
|"Root=1-628cf653-7c6a5a8f73e8842168ff0948"|"trace_id"|X-Amzn-Trace-Id 헤더의 콘텐츠가 기록|
|"-"|"domain_name"|- \[HTTPS 리스너] TLS 핸드셰이크 중에 클라이언트가 제공한 SNI 도메인<br>- 클라이언트가 SNI를 지원하지 않거나, 도메인이 인증서와 일치하지 않으면 이 값이 -로 설정되고 클라이언트에 기본 인증서가 제공|
|"-"|"chosen_cert_arn"|- \[HTTPS 리스너] 클라이언트에 제공된 인증서의 ARN<br>- 세션이 재사용되는 경우 이 값은 session-reused로 설정<br>- 리스너가 HTTPS 리스너가 아닌 경우 이 값은 -로 설정|
|0|matched_rule_priority|- 요청과 일치하는 규칙의 우선 순위 값<br>- 규칙이 일치하면 1~50,000 사이의 값<br>- 규칙이 일치하지 않고 기본 작업이 수행된 경우 이 값은 0에 설정<br>- 규칙 평가 중 오류가 발생하면 -1에 설정<br>- 기타 오류의 경우 -에 설정|
|2022-05-24T15:14:27.261000Z|request_creation_time|로드 밸런서가 클라이언트에서 요청을 받은 시간(ISO 8601 형식)|
|"forward"|"actions_executed"|- 요청을 처리할 때 수행된 작업<br>- 수행된 작업에서 설명하는 값을 포함할 수 있는 쉼표로 구분된 목록<br>- 잘못된 요청 등에 대해 수행된 작업이 없는 경우 이 값은 -로 설정|
|"-"|"redirect_url"|- HTTP 응답의 위치 헤더에 대한 리디렉션 대상 URL<br>- 수행된 리디렉션 작업이 없는 경우 이 값은 -로 설정|
|"-"|"error_reason"|- 오류 이유 코드<br>- 요청이 실패하면 이 값은 오류 이유 코드에서 설명하는 오류 코드 중 하나<br>- |수행한 작업에 인증 작업이 포함되지 않거나 대상이 Lambda 함수가 아닌 경우, 이 값은 -로 설정|
|"10.0.6.199:8088"|"target:port_list"|- 이 요청을 처리한 대상에 대한 IP 주소 및 포트를 공백으로 구분한 목록<br>- 이 목록에는 하나의 항목이 포함될 수 있으며 target:port 필드와 일치|
|"404"|"target_status_code_list"|- 대상의 응답에서 공백으로 구분된 상태 코드 목록<br>- 이 목록에는 하나의 항목이 포함될 수 있으며 target_status_code 필드와 일치|
|"-"|"classification"|- 동기화 해제 완화에 대한 분류<br>- 요청이 RFC 7230을 준수하지 않는 경우 가능한 값은 허용 가능, 모호 및 심각<br>- 요청이 RFC 7230을 준수하는 경우 이 값은 -로 설정|
|"-"|"classification_reason"|- 분류 사유 코드<br>- 요청이 RFC 7230을 준수하지 않는 경우 이 코드는 분류 이유에서 설명하는 분류 코드 중 하나<br>- 요청이 RFC 7230을 준수하는 경우 이 값은 -로 설정| 

## NLB는?
- NLB 로그에는 TCP 트래픽이 아닌 **TLS 트래픽이 있는 로그**만 포함된다
- TCP 모드에서 NLB에 대한 로그를 캡처하기 위해 NLB에 연결된 네트워크 인터페이스에 대한 흐름 로그를 활성화한 다음 흐름 로그를 S3 또는 CloudWatch에 저장할 수 있다.  
- VPC 흐름 로그를 확인해야 합니다.
- 흐름 로그를 CloudWatch Logs 또는 Amazon S3에 게시할 때 데이터 수집 및 보관 요금이 적용 됨
  
  ![image](https://user-images.githubusercontent.com/79209568/170073459-098ef277-438f-4d28-a915-9e24970b5fac.png)

> #### Flow log
> - 생성  
>   ![image](https://user-images.githubusercontent.com/79209568/170156020-c0ab819c-3d67-44ec-9dee-b37ed3d6349b.png)  
>   ![image](https://user-images.githubusercontent.com/79209568/170156045-e372441d-5bee-472a-a743-54f3bb703985.png)

