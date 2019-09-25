# DB Service

## 1. DB Service 란
### 1.1 정의
기본적으로 SELECT, INSERT, UPDATE, DELETE 쿼리를 수행한다.  
전통적인 MVC패턴의 controller 와 model 부분을 담당한다.

### 1.2 구조
DB Service(쿼리)를 작성(등록)하면 Web상에 호출가능한 서비스(POST) 로 생성된다.  
일반적으로 ajax를 통해 json을 주고받는다.

![Image Of Architecture]
(./images/02-service-database-01.png)