---
title: 파이썬 Flask 사용법 1 (기초)
date: 2020-10-13
categories: [WORK, 🐍 Python]
tags: [Python]
---

[Flask](https://flask.palletsprojects.com/)는 웹 애플리케이션 개발을 위한 파이썬 프레임워크다.

물론 파이썬으로 웹 개발을 할 때 많이 쓰이는 프레임워크로 가장 유명한 것은 [Django](https://www.djangoproject.com/)(장고)인데, Django는 너무 무겁고 기능이 많아서 복잡하다. 나처럼 웹에 대한 이해가 풍부하지 않은 초보 입장에서 사용하기 어렵다는 뜻이기도 하다. 그래서 내가 필요한 기능만 최대한 라이트하게 개발을 하기 위해서는 Flask처럼 가벼운 프레임워크가 딱이다.

본 포스팅에서는 Flask에 대한 소개와 함께 초간단 사용법을 적어보려 한다. 

## 마이크로 웹 프레임워크 Flask

Flask는 홈페이지에서도 “micro” 프레임워크라는 점을 강조하고 있다. 즉, 최소한의 구성 요소와 요구 사항을 제공하기 때문에 시작하기 쉽고 필요에 따라 유연하게 사용할 수 있다. 그렇다고 해서 완전한 기능을 갖춘 앱을 만들기에 제약이 있다는 뜻은 아니고, 오히려 쉽게 확장할 수 있도록 설계되어 있기 때문에 개발을 하는 입장에서는 본인이 필요한 도구와 라이브러리를 자유롭게 선택해서 적용 가능하다는 게 가장 큰 장점이 아닐까 싶다. (데이터베이스를 통합하거나 계정 인증 등을 포함하는 복잡한 앱 뿐만 아니라 단순한 정적 웹 사이트를 만드는 데도 유용하다.)

## Flask로 Hello, World!

일단 플라스크로 프로젝트를 할 폴더에 가상환경을 만들고, 플라스크를 설치하자.

Flask 공식 문서](https://flask.palletsprojects.com/en/1.1.x/installation/#python-version)에서도 가상환경을 통한 설치를 안내하고 있다.

플라스크 설치는 그냥 `pip install Flask`를 치면 끝. 그리고 프로젝트 폴더 안에 app.py라는 스크립트 파일을 만들어 아래 코드를 넣고 파일을 실행해보자.

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)
```

`app.run()` 괄호 안에 `debug=True`라고 명시하면 해당 파일의 코드를 수정할 때마다 Flask가 변경된 것을 인식하고 다시 시작한다. 스터디 용도로 코딩을 할 때 내용을 바로 반영해서 결과를 확인하기 편리하다.

어쨌든 이렇게 해놓고 실행을 하면 Running on [http://127.0.0.1:5000/](http://127.0.0.1:5000/)라는 메시지가 뜨는데 로컬 환경에서 5000번 포트를 통해 해당 웹 페이지를 확인할 수 있다. 헬로 월드가 찍혀 있는 걸 보게 되면 이미 Flask로 웹 사이트 만드는 게 너무나 쉽다는 감이 올 거다.

## Route (URL에 웹페이지 연결하기)

웹 브라우저에서 URL을 방문하면 서버에 요청을 보내고, 서버는 그 요청을 처리해서 브라우저에 응답을 반환하게 된다. 그 반환된 결과를 HTML 문서로 보내주면 그 웹 페이지를 브라우저가 띄워주는 거다.  
  
Flask에서는 URL을 방문 할 때 준비된 함수가 트리거되도록 바인딩 하기 위해 `route()` 데코레이터를 사용한다. 이를 라우팅이라 한다. (파이썬 데코레이터는 골뱅이@를 사용하는 건데 좀 복잡하니 설명은 생략한다. 깊이 들어가면 나도 잘 모른다.)

어쨌든 위에서 실습한 app.py를 아래와 같이 수정해보자.

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
@app.route('/home')
def home():
    return 'Hello, World!'

@app.route('/user')
def user():
    return 'Hello, User!'

if __name__ == '__main__':
    app.run(debug=True)
```

[http://127.0.0.1:5000/](http://127.0.0.1:5000/) 뒤에 “/home”이라고 붙여도 동일하게 헬로 월드가 출력되는 페이지가 뜬다. “/”이나 “/home”이나 둘 다 같은 페이지를 출력하도록 연결했기 때문에. 그리고 만약 “/user”로 접속하면 헬로 유저가 뜰 거다. Flask에서는 이렇게 쉽게 웹 페이지를 라우팅 할 수 있다.

단, 이렇게 `@app.route('/경로')` 를 쓸 때 URL 경로는 반드시 “/” (슬래시)로 시작해야 한다.

## 동적 URL 다루는 방법

위에서는 route () 데코레이터를 사용해서 고정된 URL을 뷰 함수에 바인딩하는 방법을 알아봤다. 그런데 경우에 따라 변경되는 동적 URL을 사용하려면 어떻게 해야 할까?

Flask에서는 뷰 함수에 바인딩 할 URL을 지정할 때 `<variable_name>`을 명시하는 게 가능하다. 그러면 이 변수는 뷰 함수에 인수 형태로 전달이 된다.

다시 app.py에서 이번엔 `@app.route('/user')`와 `user()`함수를 아래와 같이 수정해보자.

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
@app.route('/home')
def home():
    return 'Hello, World!'

@app.route('/user/<user_name>/<int:user_id>')
def user(user_name, user_id):
    return f'Hello, {user_name}({user_id})!'

if __name__ == '__main__':
    app.run(debug=True)
```

반환되는 값으로 f-string 포맷을 활용해서 문자열에 변수를 넣고 h1 태그를 적용해봤다. (파이썬 문자열 다룰 때는 f-string이 짱이다. 아무튼.)

이렇게 하면 "/user/유저이름/유저아이디" 형식의 URL을 입력 받았을 때 유저이름과 유저아이디를 활용한 내용의 HTML 문서를 반환할 수 있다.

그리고 URL은 문자열이기 때문에 "converter : variable_name" 구문을 사용해서 URL에서 받은 변수 타입을 변형할 수 있다. converter로 사용할 수 있는 옵션은 아래와 같다. (자세한 내용은 Flask 공식 문서 참고)

- `string` (기본값) : slash가 포함되지 않은 문자열
- `int`
- `float`
- `path` : `string`과 동일하지만 slash를 포함한다.
- `uuid` : [UUID(범용고유식별자)](https://ko.wikipedia.org/wiki/%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90) 형식

## HTML 렌더링

`@app.route()`아래 적어주는 뷰 함수에서 return하는 응답은 일반 텍스트, 데이터 등 다양한 형식이 될 수 있는데, 일반적으로는 웹 페이지에서 렌더링 할 HTML을 직접 반환하게 될 거다.

app.py에서 home()함수를 아래와 같이 수정해보자. (return 되는 값으로 HTML 형식의 문자열을 바로 넣어주었다.)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
@app.route('/home')
def home():
    return '''
    <h1>이건 h1 제목</h1>
    <p>이건 p 본문 </p>
    <a href="https://flask.palletsprojects.com">Flask 홈페이지 바로가기</a>
    '''

@app.route('/user/<user_name>/<int:user_id>')
def user(user_name, user_id):
    return f'Hello, {user_name}({user_id})!'

if __name__ == '__main__':
    app.run(debug=True)
```

실행해서 home화면에 들어가보면 문자열로 반환된 HTML이 렌더링 되어서 화면에 잘 나타나는 걸 알 수 있다. Flask 참 쉽다.

일단 Flask 기초 사용법은 여기까지.