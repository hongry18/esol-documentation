# DB Service

## 1. DB Service 란
### 1.1 정의
기본적으로 SELECT, INSERT, UPDATE, DELETE 쿼리를 수행한다.  
전통적인 MVC패턴의 controller 와 model 부분을 담당한다.

### 1.2 구조
DB Service(쿼리)를 작성(등록)하면 Web상에 호출가능한 서비스(POST) 로 생성된다.  
일반적으로 ajax를 통해 json을 주고받는다.

![Image Of Architecture](./images/02-service-database-01.png)

## 2. 사용법
### 2.1 생성
Menu > 서비스 > DB Service > 생성  
![New Item](./images/02-service-database-02.png)

### 2.2 속성
| 입력값 | 설명 |
|:---:|:---|
| DB 서비스 ID | 고유한 ID (중복불가 , 알파벳 숫자 , 특수문자('_')  30자이내) {host}/svc/db/{userName}/{DB서비스ID} 로 호출되어지는 서비스로 생성된다. |
| DB 서비스명 | 이름, 혹은 설명을 입력, 작업자가 구분하기 위해 사용 |
| 그룹 | 작업자가 구분하기 위해 사용 |
| DB 연결 ID | DB서비스에서 기본적으로 사용할 DB POOl(connection) menu > 연결정보 > DB연결 에서 생성된 pool |
| 상태 | 활성/비활성, 비활성일 경우 서비스가 제한된다. |
| 인증체크 | 사용/미사용, 미사용이면 누구나 사용 가능 하고, 발급된 Token이 유효한 경우나 로그인 세션이 있는 경우만 사용 할지를 정의한다.  |
| POOL 유형 | 전체(선택한 DB연결ID 하나만 사용 TRANSACTION가능): 모든 쿼리는 '4. DB연결 ID'에서 선택한 하나의 DB POOL을 사용한다. <br/> 개별(각각의 QUERY의 DB연결ID를 사용 TRANSACTION불가) : 쿼리별로 각각 DB POOL을 지정하여 사용 한다. |
| 쿼리 생성 | 2.3 쿼리생성 참조 |

### 2.3 쿼리 생성
DB 서비스 추가 창 > 생성
실제 쿼리를 작성한다,  쿼리를 여러개 작성 할 수 있다.  
한번의 서비스 호출로 여러개의 select결과 혹은 여러 CRUD를 실행 할 수 있다.  
Database작업은 쿼리의 실행 순서도 중요하므로 순번을 이동 할 수 있다. (마우스 드래그앤드롭)  
![Create Query Button](./images/02-service-database-03.png)
![Create Query](./images/02-service-database-04.png)

| 입력값 | 설명 |
|:---:|:---|
| Query명 | 작업자 쿼리를 구분하기 위해 사용, 예)LIST , ADD , MOD , DEL 등 |
| DB 연결 ID | 해당 쿼리가 사용할 DB Pool '7. POOL유형'에서 '개별'로 선택한 경우만 작동된다. '전체'일 경우는 '4. DB연결 ID'에서 선택된 pool을 사용 한다. |
| Query 유형 | Single : 단일 값(record)만 Array의 첫번째 record 사용한다.<br />예) Query유형이 SELECT 인경우는 필수 ,INSERT UPDATE DELETE인 경우도 1개만 사용할 경우<br />Array  : Array 전체를 사용<br />예) INSERT, UPDATE, DELETE인 경우 복수의 값을 입력할 경우 사용 |
| 입력 유형 | SELECT,INSERT,UPDATE,DELETE 를 확실히 구분 한다.<br />Query 유형에 따라 결과값을 다르게 받기 때문에 정확히 해야 한다.<br />SELECT는 ResultSet을 , 나머지는 int 혹은 int[]로 결과를 받는다. |
| read level | 'Query 유형'이 SELECT 인 경우만 해당함 transaction isolation level을 의미한다.<br />DBMS별로 확인 필요 READ_UNCOMMITED을 지원 안 하는 DBMS도 있음 (oracle)<br />예) INSERT가 계속 발생하는 테이블을 읽을 수 없는 경우 READ_UNCOMMITED로 설정하면 읽을 수 있다.<br />권장하지는 않으나 반드시 필요한 경우 |
| 실행 구분 | 'Query 유형'이 INSERT UPDATE DELETE 인 경우만 해당함<br />각각실행 : 입력값 별로 executeUpdate 실행<br />Batch 시행 : executeBatch 실행 |
| 입력 Key | 쿼리가 입력받은 json에서 입력부분 Array의 KEY값을 정의 한다.)<br />예) 입력 KEY : IN1 )<br />json data를  아래와 같은 구조로 입력한다.)<br /><pre lang="json">{"input": {"IN1": [{"USER_LOGIN": ""}]}}</pre> |
| 출력 Key | 쿼리 수행 후 결과값의 KEY값을 정의 한다. |
| 쿼리 작성 | 실제 수행될 쿼리를 작성한다.<br />입력파라미터 설명<br /> |
| 쿼리 작성<br />1. 일반변수 | PreparedStatemnet의 ? 에 입력된다. mybatis의 #{변수명} 과 유사<br />사용법 : {'#변수명#'}<br />WHERE절 부분에는 반드시 일반변수를 권장한다.
| 쿼리 작성<br />2. 치환변수 | 입력값을 해당부분에 치환(replace) 한다.  mybatis의 ${변수명} 과 유사<br />ORDER BY의 컬럼명이나 혹은 ASC/DESC 같은 일반변수로 입력이 안되는<br />경우에만 사용 한다.<br />보안상 위험 하므로 권장하지는 않으나 반드시 필요한 경우<br />사용하는 경우는 '검증'을 통해 보안상 위험을 제거하기를 권장한다.<br />사용법 : {'@변수명@’} |
| 쿼리 작성<br />3.session변수 | session변수를 DB Service에서 사용 할 수 있도록 한다.<br />session attribute의 값을 쿼리 실행시 입력하므로<br />클라이언트에서 값을 직접 입력 할 필요가 없다<br />제약사항은 session attribute에  key:String , value:String 인 값만 사용 한다.<br />사용법 : {'$세션변수명$'} |