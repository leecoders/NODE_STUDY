## Passport

passport를 알기 전에 session에 대해 알아야 한다.

### Session

- 쿠키는 브라우저에, 세션은 서버에 저장된다.
- 로그인 유지 기능을 위해 세션이 필수적이다.
  - 사용자가 로그인이 되어 있음을 알기 위해 쿠키에 사용자(브라우저 단위)에 대해 세션이 생성되어 서버에 저장되고 발급된 세션에 대한 세션 아이디가 저장된다.
  - 쿠키에 세션 아이디가 없거나 쿠키에 존재하는 세션 아이디가 서버에 없는 등의 불일치가 발생하면 로그인이 필요한 기능에 대해 사용자를 제한할 수 있다.
  - `express-session`은 브라우저 단위로 세션을 생성하고 이 세션에 대한 세션 아이디와 매칭되는 쿠키를 생성하여 브라우저로 보낸다.
- `express-session`은 세션들에 대한 정보를 저장한다. 다수의 사용자에 대해 요청이 왔을 때 각 사용자의 세션이 어떤 것인지 `express-session`은 어떻게 구분해낼까?
  - 이 과정에서 `쿠키`에 담긴 `세션 아이디`가 사용된다. `express-session` 미들웨어가 요청이 왔을 때 쿠키에 담긴 `세션 아이디`(존재 한다면)를 통해 세션에 담긴 일치하는 세션을 `req.session`으로 저장하게 된다.
- `express-session` 모듈은 내부적으로 `cookie-parser`를 사용한다.
- `express-session`은 세션 관리 시 클라이언트에 쿠키를 보낸다. 이 쿠키를 `세션 쿠키`라고 부른다.

```javascript
...
const session = require("express-session");

...
app.use(session({
    resave: false,
    saveUninitialized: false,
    secret: "secret code",
    cookie: {
        httpOnly: true,
        secure: false
    }
}));
```

- 세션 설정에 대한 옵션
  - `resave` : 요청이 왔을 때 세션에 수정 사항이 생기지 않더라도 세션을 저장할지에 대한 설정
  - `saveUninitialized` : 세션에 저장할 내역이 없더라도 세션을 저장할지에 대한 설정
    - 보통 방문자를 추적할 때 사용된다.
  - `secret` : 필수 항목. `cookie-parser`의 비밀키와 같은 역할
    - 안전하게 쿠키를 전송하기 위해 `서명`을 추가하게 되는데 이 역할을 하는 것이 `secret`
  - `cookie` : 세션 쿠키에 대한 설정
    - `maxAge`, `domain`, `path`, `expires`, `sameSite`, `httpOnly`, `secure` 등의 일반적인 쿠키 옵션이 제공된다.
  - `store` : 세션에는 `store`라는 옵션이 있음
    - 서버를 재시작하면 세션이 초기화된다는 문제가 있기 때문에 보통 DB에 세션을 저장해서 사용하게 된다.
    - 보통 `Redis`가 자주 사용된다.
- `express-session`은 `req` 객체 안에 `req.session` 객체를 생성한다.
- `req.session.destroy()`를 통해 세션을 삭제할 수 있다.

### passport

- `express-session`을 통해 로그인 기능을 구현한다고 해도 신경써야 할 작업이 많아 복잡하기 때문에 로그인 또는 기타 요청에 대해 `passport`를 사용해 검증된 절차를 거치도록 안전하게 구현할 수 있다.
-
