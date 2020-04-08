---
title: "[Nodejs]생활코딩 Nodejs-passport.js"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- Nodejs
- 생활코딩
- passport.js
- TIL
- Javascript
tags:
- Nodejs
- 생활코딩
- passport.js
- TIL
- Javascript
---

**Passport**는 Node의 인증 미들웨어이다. 일반적으로 웹 애플리케이션은 사용자의 아이디와 비밀번호를 입력 받아 인증을 한다. 
최근에는 소셜 네트워크의 등장으로 Facebook 이나 Twitter와 같은 OAuth공급자를 사용한 싱글 사인온(SSO)이 널리
사용되는 인증방법이 되었다.  
  
**strategy** 인증 메커니즘은 개별 모듈로 패키지되고, 애플리케이션은 불필요한 종속성을 만들지 않고 사용할 strategy를 선택할 수 있다.
**OAuth**, **OpneId** 를 이용 하면 사용자의 비밀번호를 제3의 서비스에 노출하지 않고도 인증기능을 구현 할 수 있다.

# passport.js 설치

**npm을 이용한 설치**  

> npm install -s passport

> npm install -s passport-local (strategy중 passport-local을 사용함.)

**사용방법**  

```javascript
const passport = require('passport'),
      LocalStrategy = require('passport-local').Strategy;
```

Passport를 사용하기 위해 세 가지를 구성해야 한다.  
1. Authentication strategies(인증 전략)  
2. Application middleware(응용 프로그램 미들웨어)  
3. Session (optional)(세션(선택사항))  

**Strategies**  
```javascript
var passport = require('passport')
  , LocalStrategy = require('passport-local').Strategy; // Passport-local방식을 사용

passport.use(new LocalStrategy(
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      if (!user.validPassword(password)) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      return done(null, user);
    });
  }
));
```

strategy는 passport가 구현하는 **인증방식**이다. **Passport-local**은 Username/Password로 로그인을 직접 구현할 때 사용된다.  
이외에 passport-google-oauth, passport-facebook, passport-twitter, passport-kakao, passport-naver 등 OAuth 인증으로
로그인할 수 있는 패키지 모듈도 있다.  
  
.use()로 passport를 설정 및 사용 한다.  
  
반드시 세션을 활성화하는 코드 이후에 passport 코드를 위치시킨다.  
  
**Verify Callback**  
  
strategy는 verify callback이 필요하다. 사용자의 요청에 담긴 정보를 인자로해서 callback을 호출해서 유효한 값이면   
>  return done(null, user);  
첫 번째 인자인 null에는 DB조회 시 발생하는 서버에러의 값, 두 번째는 인증이 유효한 값일때 반환할 값을 넣는다.  
  
> return done(null, false, { message: 'Incorrect password.' });  
사용자의 요청에 담긴 인증 정보가 일치 하지 않을 때 return문.  
세 번째 파라미터인 flash message는 옵션이다.  
  
세션의 올바른 복원을 위해 session()을 passport.session() 전에 사용해야한다.  

**Middleware**

```javascript
app.configure(function() {
  app.use(express.static('public'));
  app.use(express.cookieParser());
  app.use(express.bodyParser());
  app.use(express.session({ secret: 'keyboard cat' }));
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
});
```
세션의 올바른 복원을 위해 session()을 passport.session() 전에 사용해야한다.

**Session**
인증에 성공하면 사용자 브라우저에서 설정된 쿠키를 통해 session이 설정되고 유지된다.
이후의 요청에는 인증정보가아니라 session을 식별하는 고유한 cookie가 포함된다
로그인 세션을 지원하기위해 session과 user인스턴스를 **serializeUser**  및 **deserializeUser** 를 할 수 있다.

```javascript
passport.serializeUser(function(user, done) {
  done(null, user.id);
});

passport.deserializeUser(function(id, done) {
  User.findById(id, function(err, user) {
    done(err, user);
  });
});
```
**serializeUser** - 로그인애 성공했을때 호출되어 사용자의 식별자를 session store에 저장한다.  

**deserializeUsenr** - 로그인 후 페이지 방문할때마다 호출되어 id값으로 사용자의 데이터를 가져온다.  


# passport.js 인증 구현
로그인 폼에서 전송한 데이터를 받는 쪽을 passport로 전환

```javascript
app.post('/auth/login_process',
  passport.authenticate('local', {
      successRedirect : '/',//로그인이 됐을때
      failureRedirect: '/auth/login'//로그인이 안됐을때
  })
)
```


# passport.js 자격확인
전송된 정보가 유효한지 여부를 파악하는 방법
```javascript
module.exports = function(app){

var authData = {
    email:"egoing777@gmail.com",
    password:"111111",
    nickname:"egoing"
  }

//passport 설치
const passport = require('passport'),
  LocalStrategy = require('passport-local').Strategy;

  //passport를 express에 설치
app.use(passport.initialize());
//session을 사용하겠다는 것.
app.use(passport.session());

passport.serializeUser(function(user,done){//로그인애 성공했을때 호출되어 사용자의 식별자를 session store에 저장한다.
  console.log('serializeUser: ', user);
  done(null, user.email);//두번째인자는 사용자의 식별자
});

passport.deserializeUser(function(id,done){//로그인 후 페이지 방문할때마다 호출됨. id값으로 사용자의 데이터를 가져옴.
  console.log('deserializeUser: ', id);
  done(null, authData);
})

passport.use(new LocalStrategy(
  {
    usernameField: 'email',
    passwordField: 'password'
  },
  function (username, password, done){
    console.log('LocalStrategy: ', username+', '+password);
    if(username === authData.email){
      console.log(1);
      if(password === authData.password){
        console.log(2);
        return done(null,authData,{
          message: 'Welcome.'
        });
      }else{
        console.log(3);
        return done(null,false,{
          message: 'Incorrect password.'
        })
      }
    }else{
      console.log(4);
      return done(null,false,{
        message: 'Incorrect username.'
      });
    }
}
))
return passport;
}


```
<br/>

# passport.js 로그인  및 로그아웃   
```javascript
const express = require('express');
const router = express.Router();
const template = require('../lib/template.js')

module.exports = module.exports = function(passport){
  router.get('/login',function(request,response){
    var fmsg = request.flash();
    var feedback = '';
    if(fmsg.error){
      feedback = fmsg.error[0];
    }
  
    var title = 'WEB - login';
    var list = template.list(request.list);
    var html = template.HTML(title, list, `
      <div style="color:red">${feedback}</div>
    <form action="/auth/login_process" method="post">
        <p><input type="text" name="email" placeholder="email"></p>
        <p>
          <input type="password" name="password" placeholder="password">
        </p>
        <p>
          <input type="submit" value="login">
        </p>
      </form>
    `, '');
    response.send(html);
  
  })
  
//로그인
  router.post('/login_process',
    passport.authenticate('local', {
        successRedirect : '/',//로그인이 됐을때
        failureRedirect: '/auth/login',//로그인이 안됐을때
        failureFlash: true,
        successFlash: true
    })
  )
  
//로그아웃
  router.get('/logout',function(request,response){
 
    request.logout();
    request.session.save(function(){
      response.redirect('/');
    })
  })
  return router;
};


```

**main.js**
```javascript
const express = require('express')
const app = express(); //express는 application 객체를 리턴해준다
const fs = require('fs');
const bodyParser = require('body-parser');
const compression = require('compression');
const session = require('express-session');
const FileStore = require('session-file-store')(session);
const flash = require('connect-flash');

app.use(express.static('public'));
app.use(bodyParser.urlencoded({extended: false}));
app.use(compression());//미들웨어를 리턴해서 app.use에 장착
app.use(session({
  secret : 'keyboard cat',
  resave : false,
  saveUninitialized: true
  // store: new FileStore()
}))

app.use(flash());//flash는 내부적으로 session을 쓰기때문에 session다음에 미들웨어를 실행해야함
app.get('/flash', function(req,res){
  //set a flash message by passing the key, followed by the value, tothe req.flash()
  req.flash('info','Flash is back!');
  // res.redirect('/');
  res.send('flash');
})

app.get('/flash-display', function(req,res){
  var fms = req.flash();
  res.send(fms)
})

var passport = require('./lib/passport')(app);



app.get('*',function(request, response, next){
  fs.readdir('./data',function(error,filelist){
    request.list = filelist;
    next();//다음 미들웨어를 실행함.
  });
});

const indexRouter = require('./routes/index')
const topicRouter = require('./routes/topic');
const authRouter = require('./routes/auth')(passport);
app.use('/',indexRouter);
app.use('/topic',topicRouter);
app.use('/auth',authRouter);

//없는 페이지를 찾을 때 에러 처리
app.use(function(req,res,next){
  res.status(404).send('Sorry cant find that !');
})

//에러를 핸들링 하는 미들웨어
app.use(function(err,req,res,next){
  console.error(err.stack);
  res.status(500).send('Somthing broke!');
})

// app.listen(3000,()=>console.log('Example app listening on port 3000'));
app.listen(3000,function(){
  console.log('Example app listening on port 3000 ~');
})

```

# passport.js 플래쉬 메시지

1회용 메시지인 flash message 모듈인 connect-flash의 기본 사용법
**npm을 이용한 설치**  

> npm install -s  connect-flash

**사용방법**  
```javascript
app.use(flash());//flash는 내부적으로 session을 쓰기때문에 session다음에 미들웨어를 실행해야함
app.get('/flash', function(req,res){
  //set a flash message by passing the key, followed by the value, tothe req.flash()
  req.flash('info','Flash is back!');
  // res.redirect('/');
  res.send('flash');
})

app.get('/flash-display', function(req,res){
  var fms = req.flash();
  res.send(fms)
})
```
 ```javascript
 passport.authenticate('local', {
        successRedirect : '/',//로그인이 됐을때
        failureRedirect: '/auth/login',//로그인이 안됐을때
        failureFlash: true,
        successFlash: true
    })
```
failureFlash와 successFlash 의 값을 각각 true로 주면,  
done() 메소드의 세 번째 파라미터로 전달된 message의 value 값을 받을 수 있다.  
  
  
**마무리**
passport의 진정한 가치는 300여개의 인증전략, 보안전문가가 아닌 개발자들이 쉽게 보안을 구현할 수 있음.  
=> 예를 들면 federated identity(페북 로그인, 카톡 로그인 등)