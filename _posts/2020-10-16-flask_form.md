---
title: 파이썬 Flask 사용법 3 (form)
date: 2020-10-16
categories: [WORK, Python]
tags: [Python]
---

웹 사이트에서 빠뜨릴 수 없는 기능 중 하나가 바로 사용자로부터 정보를 수집하는 거다. 계정에 로그인하고, 상품을 주문하거나 설문조사를 하는 등. 이렇게 사용자로부터 정보를 입력 받는 방식을 form이라 한다.

form을 사용하더라도 막상 정보를 깔끔하고 체계적으로 수집하려면 만만치가 않은데, 파이썬 웹 프레임워크 Flask(플라스크)를 사용하면 필드를 표시하고 데이터를 쉽게 수집하는 절차를 초보자도 깔끔히 처리할 수 있다.  
  
이 포스팅에서는 웹 form에 대한 기초 지식과 함께 Flask에서 어떻게 form 데이터를 수집하고 처리하는지 (회원가입 폼 양식 예시를 통해) 최대한 쉽게 소개하려 한다.

## Flask에서 form 활용을 위한 프로젝트 폴더, 파일 관리

자, 이제 Flask(플라스크)로 웹 앱을 만들기 위한 프로젝트 폴더 내에서 파일 구조를 어떻게 가져가면 편한지 간단한 형태로 구성해서 설명해야겠다.

- `forms.py` : form을 활용하기 위한 클래스를 담아놓는 파일. 유효성 검사와 같은 기능을 포함해놓을 수 있고, 이후에 flask 앱에서는 여기서 미리 생성해놓은 form 클래스를 가져다 쓰기만 하면 된다.
- `app.py` : Flask 앱을 구동시킬 실행시킬 파일. URL와 템플릿을 연결하는 route 데코레이터와 그에 해당하는 뷰 함수가 담겨 있다.
- `*.html` : 모든 HTML 문서, 즉 템플릿 파일은 templates라는 폴더 안에 담아놓아야 한다. 이 템플릿 내에서 내가 쓸 폼을 플라스크 변수 형태로 담아 사용할 수 있다.

## (1) FlaskForm을 활용하여 클래스 만들기 (forms.py)

프로젝트 폴더 내에서 forms.py를 만들고 아래와 같이 회원 가입을 위한 form을 구성하는 파일을 만들어보자.

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Length, Email, EqualTo

class RegistrationForm(FlaskForm):
    username =  StringField("아이디", 
                            validators=[DataRequired(), Length(min=4, max=20)])
    email =  StringField("이메일", 
                            validators=[DataRequired(), Email()])
    password = PasswordField("비밀번호", 
                            validators=[DataRequired(), Length(min=4, max=20)])
    confirm_password = PasswordField("비밀번호 확인", 
                            validators=[DataRequired(), EqualTo("password")] )
    submit = SubmitField("가입")
```

- 일단 `FlaskWTF`의 `FlaskForm`를 불러오는데, 내가 원하는 form을 만들기 위해서는 `FlaskForm`이라는 부모 클래스를 상속 받아 자식 클래스를 만들어 사용하면 된다. (자식클래스를 선언할때 소괄호로 부모클래스를 포함시키게 되면 자식클래스에서는 부모클래스의 속성과 메소드는 기재하지 않아도 알아서 포함이 된다.)
- 그리고 이와 함께 `wtforms`라는 라이브러리를 사용한다. 예를 들면 문자열인지(StringField), 화면에 표시되지 않아야 할 패스워드인지(PasswordField), 제출 버튼인지(SubmitField)에 따라 원하는 폼을 지정할 수 있고, 이 후 `wtforms.validators`를 통해 필수입력값인지(DataRequired), 길이는 어떻게 제한하는지(Length), 이메일인지(Emil), 이미 입력한 값과 같은 값을 입력했는지(EqualTo) 등의 유효성 검사를 할 수 있다.

예시로 생성한 클래스를 보면 일단 폼의 종류와 함께, 그 입력란의 label이 무엇인지 적어주고, validators를 통해 유효성 검사 항목을 포함해주면 끝이다.

## (2) GET, POST 요청 처리에 따라 실행될 뷰 함수 (app.py)

웹에서는 클라이언트가 서버와 통신 할 때마다 요청(request)이라는 걸 하게 된다. Flask에서는 웹 페이지를 route하게 되면 기본적으로 GET 요청만 지원한다. 해당 URL에 접속했을 때 브라우저 창에 어떤 내용을 표시할지 요청하는 거다. 만약 웹 사이트를 통해 사용자가 form을 입력해서 그 양식을 제출하면 POST 요청으로 전송된다. 이 경우에는 `route()` 데코레이터의 `method` 인수로 POST를 지정해서 그 요청을 처리할 수가 있다. 이렇게.

```python
@ app.route ( "/", methods = [ "GET", "POST"])
```

위와 같이 하면 GET 요청과 POST 요청 모두 처리할 수 있는 거다. (`method`를 지정하지 않으면 기본값은 GET이다.)

실제로 위에서 만든 forms.py의 클래스를 가져와 회원가입 기능을 app.py에 연결하려면 아래와 같이 적어주면 된다.

```python
from flask import Flask, render_template, url_for, flash, redirect
from forms import RegistrationForm

app = Flask(__name__)
app.config["SECRET_KEY"] = 'd2707fea9778e085491e2dbbc73ff30e'

@app.route('/')
def home():
    return render_template('layout.html')

@app.route('/register', methods=["GET", "POST"])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        # 알람 카테고리에 따라 부트스트랩에서 다른 스타일을 적용 (success, danger) 
        flash(f'{form.username.data} 님 가입 완료!', 'success')
        return redirect(url_for('home'))
    return render_template('register.html', form=form)

if __name__ == '__main__':
    app.run(debug=True)
```

- `from forms import RegistrationForm`로 일단 forms.py에서 작성한 내 회원가입 폼을 불러왔다. 곧 이어서 `app.config["SECRET_KEY"] = "복잡한 문자열"`라는 코드가 보이는데, 이건 CSRF(Cross-Site Request Forgery)라고 웹 애플리케이션을 공격하는 걸로부터 보호하기 위해 사용하는 거다. 일단 아무거나 적어주자. (뒤에서 HTML 템플릿 구성할 때 이어서 설명한다.) 어쨌든 난 SECRET_KEY 값을 생성할 때 파이썬 secrets 모듈을 활용했다. `import secrets` 한 후 `secrets.token_hex(16)`라고 적어주면 이렇게 복잡한 문자열이 하나 생성된다.
- ‘/register’라는 URL을 회원가입으로 사용하기 위해 `methods=["GET", "POST"]`을 사용해 데코레이터를 만들어 라우팅 시킨 후 그 아래에 `register()`라는 뷰 함수를 작성했다. 일단 `form`이라는 인스턴스를 생성해놓았다.
    1. 일단 클라이언트가 ‘/register’ URL에 그냥 접속했다면 (폼을 작성해 POST 요청한 게 아니라 그냥 GET 요청) 한 것이기 때문에 `render_template('register.html', form=form)`을 돌려줘야 한다. 회원가입 페이지를 바로 띄워주는 거다. 이때 form이라는 템플릿 변수에다가 미리 생성한 `form` 인스턴스를 연결했다.
    2. 만약 클라언트가 POST 요청을 통해 form을 정상적으로 제출했다면 if 조건문 `form.validate_on_submit()`으로 확인해서 요청을 처리해야 한다. 여기서는 처리한 후 `redirect(url_for('home'))`을 반환하여 `home`이라는 뷰 함수와 연결된 곳, 즉 루트 URL로 리다이렉트 시켰다. (`url_for()` 안에는 URL을 써주는 게 아니라 뷰 함수이름을 써줘야 한다.)

그리고 `flash`는 부트스트랩을 사용해서 알림 메시지를 띄우기 위해 사용한 건데, 굳이 안 써도 된다. 메시지와 함께 카테고리 success나 danger 중 하나를 써주면 초록색, 혹은 붉은 색으로 HTML 문서에 알림을 띄울 수 있다. (이후에 템플릿을 통해 확인해보자.)

어쨌든 위와 같이 작성하고 나면 이제 실제 HTML 템플릿을 작성해야 한다.

## (3) form이 포함된 템플릿 작성 (register.html)

Jinja2 템플릿을 활용하는 거기 때문에 register.html에는 원래 이렇게 써주면 된다.

{% raw %}
```html
<form action="/" method="post">

    {{ form.hidden_tag() }}

    {{ form.username.label }}
    {{ form.username() }}

    {{ form.email.label }}
    {{ form.email() }}

    {{ form.password.label }}
    {{ form.password() }}

    {{ form.confirm_password.label }}
    {{ form.confirm_password() }}

    {{ form.submit() }}

</form>
```
{% endraw %}

그러면 실제로 렌더링 된 HTML 파일은 이런 식으로 나타날 거다.

```html
<form action="/" method="post">

    <input id="csrf_token" name="csrf_token" type="hidden" value="IjRiNzcxYTc5Mjg1NDNkZWY2MTJlZDgzOTlkMzdkN2U5NDBhMGVjNmEi.X4mQ0w.0DG_S08r-lH5AmF0XTAxRhYwngo">

    <label for="username">아이디</label>
    <input id="username" name="username" required="" type="text" value="">

    <label for="email">이메일</label>
    <input id="email" name="email" required="" type="text" value="">

    <label for="password">비밀번호</label>
    <input id="password" name="password" required="" type="password" value="">

    <label for="confirm_password">비밀번호 확인</label>
    <input id="confirm_password" name="confirm_password" required="" type="password" value="">

    <input id="submit" name="submit" type="submit" value="가입">

</form>
```

- 첫번째 {% raw %}`{{ template_form.hidden_tag() }}`{% endraw %}는 화면에 표시되지는 않지만 CSRF로부터 보호하는 데 필요한 작업을 처리한다. 그래서 실제 HTML 문서를 보면 복잡한 코드가 자동으로 포함된다.
- 이후에 {% raw %}`{{ form.username.label() }}`{% endraw %}과 같은 식으로 레이블을 가져오고, 이어서 양식을 작성할 부분에는 {% raw %}`{{ form.username }}`{% endraw %}이라고 적어주면 그만이다.

### ※ 템플릿 상속, 스타일 적용

위와 같이 하면 너무 단순하니까 부트스트랩을 활용해서 조금 꾸며보자. (아까 구성했던 알림 메시지도 띄워보고, validator로 에러 메시지도 띄워보고.

일단 layout.html이라는 레이아웃 템플릿을 만들고, 회원가입 페이지에 활용할 register.html에서는 이 레이아웃을 상속 받아 사용할 거다.

layout.html

{% raw %}
```html
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
</head>

<body>
    <main role="main" class="container">
        <div class="row">
            <div class="col-md-8">

                {% with messages = get_flashed_messages(with_categories=true) %}
                    {% if messages %}
                        {% for category, message in messages %}
                            <div class="alert alert-{{ category }}">
                                {{ message }}
                            </div>
                        {% endfor %}
                    {% endif %}
                {% endwith %}

                {% block content %}
                {% endblock %}
            </div>
        </div>
    </main>
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ho+j7jyWK8fNQe+A12Hb8AhRq26LrZ/JpcUGGOn+Y7RsweNrtN/tE3MoK7ZeZDyx" crossorigin="anonymous"></script>
</body>
</html>
```
{% endraw %}

일단 부트스트랩 스타일을 활용을 위해 [여기](https://getbootstrap.com/docs/4.5/getting-started/introduction/#quick-start)에 있는 코드를 `<link>`태그로 붙여넣어놨다. 그리고 2가지만 보면 되는데,

- {% raw %}`{% with messages = get_flashed_messages(with_categories=true) %}`{% endraw %} 라는 with 문을 활용해, 만약 메시지가 있을 경우 카테고리(success, danger)에 따라 출력되도록 했다.
- {% raw %}`{% block content %}`{% endraw %}와 {% raw %}`{% endblock %}`{% endraw %} 사이에 상속받을 HTML 파일 내용을 넣어줄 예정이다.

이제 실제 register.html 파일은 이렇게 구성된다.

{% raw %}
```html
{% extends "layout.html"  %}

{% block content %}

    <div class="content-section">
        <form method="post" action="">

            {{ form.hidden_tag() }}
            
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">가입하기</legend>

                <div class="form-group">
                    {{ form.username.label(class="form-control-label") }}

                    {% if form.username.errors %}
                        {{ form.username(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.username.errors %}
                                <span>{{ error }}</span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.username(class="form-control form-control-lg") }}
                    {% endif %}
                </div>

                <div class="form-group">
                    {{ form.email.label(class="form-control-label") }}
                    {% if form.email.errors %}
                        {{ form.email(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.email.errors %}
                                <span>{{ error }}</span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.email(class="form-control form-control-lg") }}
                    {% endif %}
                </div>

                <div class="form-group">
                    {{ form.password.label(class="form-control-label") }}
                    {% if form.password.errors %}
                        {{ form.password(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.password.errors %}
                                <span>{{ error }}</span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.password(class="form-control form-control-lg") }}
                    {% endif %}
                </div>

                <div class="form-group">
                    {{ form.confirm_password.label(class="form-control-label") }}
                    {% if form.confirm_password.errors %}
                        {{ form.confirm_password(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.confirm_password.errors %}
                                <span>{{ error }}</span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.confirm_password(class="form-control form-control-lg") }}
                    {% endif %}
                </div>
                
            </fieldset>

            <div class="form-group">
                {{ form.submit(class="btn btn-outline-info") }}
            </div>

        </form>
    </div>

{% endblock content %}
```
{% endraw %}

이러면 좀 더 예쁜 화면이 등장한다.

파이썬 Flask에서 form 활용하기. 재밌다.