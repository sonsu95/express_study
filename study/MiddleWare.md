## 미들웨어(middleware)란?
    Express.js에서 핵심적인 개념.
    요청(request)과 응답(response) 객체 사이에 위치하여 요청-응답 처리 과정 중간에 특정 코드를 실행하도록 하는 함수 또는 함수들의 집합을 말한다.
    Express.js의 미들웨어는 크게 어플리케이션 레벨 미들웨어와 라우터 레벨 미들웨어로 구분된다.

### 1. Application-level middleware<br>

    Express 어플리케이션 객체에 바인드되는 미들웨어
    app.use() 및 app.METHOD() 함수를 사용하여 어플리케이션에 추가된다.
    이 미들웨어는 앱의 모든 요청에 대해 실행될 수 있다. 즉 모든 요청에 대해 검사를 수행하는 것. 

for example...
```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
  console.log('이 미들웨어는 모든 요청에 대해 실행됩니다.');
  next();
});
```

### 2. Router-level middleware
    Express의 라우터 인스턴스에 바인드되는 미들웨어
    router.use() 및 router.METHOD() 함수를 사용하여 라우터에 추가된다.
    특정 경로에 대한 요청이나 특정 라우터 인스턴스에만 적용될 미들웨어를 정의할 때 사용한다.

for example...
```javascript
const express = require('express');
const router = express.Router();

router.use((req, res, next) => {
  console.log('이 미들웨어는 이 라우터에 바인드된 요청에만 실행됩니다.');
  next();
});

app.use('/specific-path', router);
```


#### 미들웨어들은 그 자체로는 어플리케이션 레벨인지, 라우터 레벨인지 구분할 수 없다.<br> 
#### 미들웨어가 어플리케이션 레벨인지, 라우터 레벨인지 구분하는 방법은 app.use() 함수를 사용하는지, router.use() 함수를 사용하는지에 따라 구분된다.
#### 즉, 사용하기에 따라 어플리케이션 레벨인지 라우터 레벨인지 구분된다.

<br>

좀 더 이해하기 쉽게 예시를 달자면 

### application-level middleware
```javascript
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));
app.use(cookieParser());
app.use(morgan('dev'));
app.use(cors());
app.use(helmet());
```

### router-level middleware
```javascript
const userRouter = express.Router();

userRouter.use(cookieParser()); // 사용자 관련 라우트에서만 쿠키 파싱
userRouter.use(morgan('dev'));  // 사용자 관련 라우트에 대한 로깅만 수행

app.use('/users', userRouter);
```

## MiddleWare를 사용하는 경우 

미들웨어를 사용하는 경우는 다음과 같다.<br>

### 1. 모듈화와 재사용성
    미들웨어는 특정 기능을 수행하는 독립적인 단위로 작성될 수 있어서, 코드를 모듈화하고 재사용하기 쉽게 만들어준다.
    예를 들어, 로깅, 에러 핸들링, 요청 본문 파싱 등의 공통 기능을 미들웨어로 작성하면 다양한 위치에서 이를 재사용할 수 있다.

### 2. 요청-응답 생명주기에서의 중간 처리
    미들웨어는 이름에서도 알 수 있듯이 요청과 응답 사이의 중간 단계에서 작동한다. 
    이를 통해 요청 데이터의 전처리, 응답 데이터의 후처리, 조건부 라우팅 등의 다양한 중간 작업을 쉽게 처리할 수 있다.

    이 경우, DDOS 공격 같은 방식에서 허용된 ip 만들 서버에 보내주는 방식으로 서버를 보호할 수 있다.


### 3. 코드의 순차적 실행
    미들웨어는 순차적으로 실행되므로, 특정 순서대로 코드를 실행하고 싶을 때 유용하다. 
    예를 들어, 로깅을 먼저 수행한 후, 인증을 체크하고, 그 다음에 요청 데이터를 파싱하는 등의 순차적인 작업을 구현할 수 있다.

### 4. 에러 핸들링
    미들웨어를 사용하면 애플리케이션 전체에서 발생하는 에러를 중앙에서 처리할 수 있다. 
    특별한 에러 핸들링 미들웨어를 구현하여 에러 발생 시 적절한 응답을 클라이언트에게 전달하거나 로깅을 수행할 수 있습니다.

### 5. 확장성
    필요에 따라 새로운 미들웨어를 쉽게 추가하거나 기존 미들웨어를 제거할 수 있다. 
    이를 통해 애플리케이션의 기능을 유연하게 확장하거나 변경할 수 있다.

### 6. 통합성
    다양한 외부 라이브러리나 서비스를 미들웨어 형태로 통합할 수 있다. 
    예를 들어, 데이터베이스 연결, 세션 관리, 인증 및 인가 등의 외부 라이브러리를 미들웨어로 쉽게 통합하여 사용할 수 있다.

<br>

#### 결론적으로, 미들웨어는 Express.js와 같은 웹 프레임워크에서 코드의 모듈화, 재사용성, 확장성을 높이고, 요청-응답 생명주기를 효율적으로 관리하기 위한 도구라고 할 수 있다.


---

## 미들웨어의 종류

### 1. express.json()
    JSON 형식의 본문을 파싱하는 미들웨어
```javascript
app.use(express.json());
```

### 2. express.urlencoded({ extended: true })
    URL-encoded 형식의 본문을 파싱하는 미들웨어
    extended 옵션은 객체 내의 배열과 같은 복잡한 데이터 구조를 파싱할 수 있게 해준다.
```javascript
app.use(express.urlencoded({ extended: true }));
```

### 3. cors
    Cross-Origin Resource Sharing(CORS) 설정을 위한 미들웨어
    브라우저에서 다른 도메인의 API를 안전하게 요청할 수 있게 해준다.
```javascript
const cors = require('cors');
app.use(cors());
```

### 4. helmet
    여러 보안 관련 HTTP 헤더를 설정해주는 미들웨어
```javascript
const helmet = require('helmet');
app.use(helmet());
```

### 5. dayjs
    경량화된 JavaScript 날짜 및 시간 라이브러리
    moment.js와 유사한 API를 제공하지만, 훨씬 더 작은 크기로 제공되어 브라우저 및 서버 환경에서 모두 사용하기에 적합하다.
```javascript
const dayjs = require('dayjs');
const today = dayjs();
console.log(today.format('YYYY-MM-DD'));
```

### 6. nodemon
    Node.js 애플리케이션의 개발을 도와주는 유틸리티. 
    소스 파일이 변경될 때마다 자동으로 애플리케이션을 재시작해준다.
```json lines
nodemon server.js
```

### 7. express.static()
    정적 파일을 제공하는 미들웨어
    정적 파일이란, 프로그램 실행 중에 내용이 변경되지 않는 파일을 말한다.
    예를 들어, HTML, CSS, JavaScript, 이미지 파일 등이 있다.
    정적 파일을 제공할 때는 express.static() 미들웨어를 사용한다.
    이 미들웨어는 정적 파일들이 담겨 있는 폴더를 지정하면 된다.
```javascript
app.use(express.static('public'));
```

### 8. cookie-parser
    쿠키를 파싱하는 미들웨어
    쿠키는 클라이언트 측에 저장되는 키-값 쌍의 작은 데이터 파일이다.
    쿠키는 브라우저의 메모리에 저장되기 때문에, 브라우저를 종료하면 쿠키도 함께 삭제된다.
    쿠키는 주로 세션 관리, 개인화, 트래킹 등을 위해 사용된다.
```javascript
const cookieParser = require('cookie-parser');
app.use(cookieParser());
```

### 9. axios
    HTTP 클라이언트 라이브러리
    Node.js 환경에서 HTTP 요청을 보낼 때 사용한다.
    즉, 다른 서버에 요청을 보낼 때 사용.
```javascript
const axios = require('axios');

axios.get('https://api.example.com/data').then(response => {
  console.log(response.data);
});
```

### 10. bcrypt
    비밀번호를 암호화하는 라이브러리
    사용자의 비밀번호를 암호화하여 데이터베이스에 저장할 때 사용한다.
```javascript
const bcrypt = require('bcrypt');

const password = 'user_password';
bcrypt.hash(password, 10).then(hash => {
  console.log('Hashed Password:', hash);
});
```

### 11. jsonwebtoken
    JSON Web Token(JWT)을 생성하고 검증하는 라이브러리
    JWT는 사용자의 인증 정보를 안전하게 전달하기 위해 사용한다.
```javascript
const jwt = require('jsonwebtoken');

const token = jwt.sign({ userId: 12345 }, 'secret_key');
const decoded = jwt.verify(token, 'secret_key');
console.log('Decoded Data:', decoded);
```