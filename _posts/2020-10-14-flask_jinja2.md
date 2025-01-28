---
title: 파이썬 Flask 사용법 2 (Jinja2 템플릿)
date: 2020-10-14
categories: [WORK, 🐍 Python]
tags: [Python]
---

이전 글에서 파이썬 Flask(플라스크) 초간단 사용법을 익혀봤다. 그 글에서는 클라이언트에게 돌려줄 웹 페이지로 단순한 HTML 형식의 문자열을 작성하는 예시를 사용했다. 물론 이렇게 해도 웹페이지가 잘 나타나긴 하지만 만약 제대로 된 웹 사이트, 즉 애플리케이션이라는 걸 만들고자 한다면 모든 페이지마다 HTML 파일을 각각 작성할 게 아니라 일관된 구조와 기능을 가진 템플릿(template)을 활용해야 한다.

Flask에서는 [Jinja2](https://jinja.palletsprojects.com)라는 템플릿 엔진을 사용해서 애플리케이션 내 변수와 반복문, 조건문 등을 포함하는 HTML 파일을 렌더링할 수 있다. 그래서 이번 글에서는 Jinja2 사용법과 함께 템플릿 내에서 변수, 조건문/반복문 사용법에 대해 익혀보려 한다.

## 템플릿 렌더링해서 URL에 연결하기

일단 프로젝트 폴더 내에는 app.py라는 파일을 아래와 같이 작성해보자.

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    return render_template("index.html")

if __name__ == '__main__':
    app.run(debug=True)
```

그리고 프로젝트 폴더 내에 templates라는 폴더를 생성하고 (폴더 이름은 반드시 templates여야 한다.)

그 안에 index.html이라는 파일을 생성하고 아래와 같이 넣어보자.

```html
<!DOCTYPE html>
<html>
<body>
    <h1>메인 화면</h1>
</body>
</html>
```

그리고 app.py를 실행하면 Running on [http://127.0.0.1:5000/](http://127.0.0.1:5000/)라는 메시지가 뜨는데 (로컬 환경에서 5000번 포트를 사용한다는 뜻) 웹 브라우저로 여기 들어가보면 된다.

그러면 첫 화면이 index.html을 띄워줄 거다. `@app.route('/')`에 바인딩 된 함수가 `render_template("index.html")`이라고 되어 있기 때문에 해당 문서를 렌더링해서 반환하는 거다. Flask 참 쉽다.

## 템플릿에서 변수 사용하기 (filter 기능 활용)

템플릿 내에서 변수를 사용하려면 `render_template()` 괄호 안에 렌더링 할 HTML 파일 이름에 이어 변수명을 키워드 인수를 추가해주면 된다. 둘 이상의 변수를 추가하려면 쉼표로 구분하면 그만이다.

실제로 템플릿에서 이 변수를 사용할 때는 {% raw %}`{{ 변수명 }}`{% endraw %}과 같은 식으로 불러와서 쓰면 된다.

그리고 이 변수를 템플릿 내에서 파이썬 함수처럼 filter 기능으로 처리하는 방법도 있다. {% raw %}`{{ 변수명 | 필터 }}`{% endraw %}와 같은 식으로 써주면 된다. ([Jinja2 공식 문서](https://jinja.palletsprojects.com/en/2.11.x/templates/#builtin-filters) 참고)

## 템플릿에서 if 조건문 사용하기

if 조건문은 이런 형식으로 바로 써줄 수 있다.

{% raw %}
```html
{% if template_variable == "Hello" %}
  <p>{{ template_variable }}, World!</p> 
{% endif %}
```
{% endraw %}

elif와 else도 파이썬에서 직접 사용하는 것과 같은 문법으로 사용할 수 있다.

{% raw %}
```html
{% if template_variable < 20 %}
  <p>{{ template_variable }}은 20보다 작다.</p> 
{% elif template_variable > 20 %}
   <p>{{ template_variable }}은 20보다 크다.</p> 
{% else %}
   <p>{{ template_variable }}은 20이다.</p> 
{% endif %}
```
{% endraw %}

## 템플릿에서 for 반복문 사용하기

for 반복문도 마찬가지다.

{% raw %}
```html
<ul>
{% for x in range(10) %}
    <li>{{ x }}</li>
{% endfor%}
</ul>
```
{% endraw %}

딕셔너리도 당연히 활용 가능하다.

{% raw %}
```html
<ul>
{% for key, value in template_dict.items() %}
    <li>{{ key }} : {{ value }}</li>
{% endfor%}
</ul>
```
{% endraw %}

`dictsort` 필터를 사용할 때는 (뒤에 굳이 `.items()`를 붙여주지 말고) 이런 식으로 써야 하더라. 이것 때문에 삽질 좀 했다.

{% raw %}
```html
<ul>
{% for key, value in template_dict | dictsort %}
    <li>{{ key }} : {{ value  }}</li>
{% endfor%}
</ul>
```
{% endraw %}

## 템플릿 상속 (Inheritance)

웹 사이트 레이아웃의 일관성을 유지하거나, header와 footer를 여러곳에 사용하기 위해서는 템플릿 상속 기능을 사용하면 된다.

- 부모문서를 만들고, 자식문서가 들어갈 부분에 {% raw %}`{% block content %}`{% endraw %} {% raw %}`{% endblock %}`{% endraw %}라고 작성한다.
- 자식문서 윗부분에 {% raw %}`{% extends 부모문서이름 %}`{% endraw %}라고 명시한 후 {% raw %}`{% block content %}`{% endraw %}와 {% raw %}`{% endblock %}`{% endraw %}사이에 내용을 작성한다.

예를 들어 base.html이라는 부모문서를 이렇게 작성하고

{% raw %}
```html
<html>
  <head>
    <title>내 웹사이트</title>
  </head>
  <body>
  {% block content %}{% endblock %}
  </body>
</html>
```
{% endraw %}

index.html이라는 이름으로 자식 문서를 만들면, 그 자식문서에는 이렇게 적어주면 되겠다.

{% raw %}
```html
{% extends "base.html"  %}

{% block content %}
    <p>자식문서에 포함될 내용</p>
{% endblock %}

```
{% endraw %}

실제로 index.html이라는 문서를 `render_template("index.html")`와 같이 렌더링 해서 확인하면 아래와 같이 구성이 된다는 걸 알 수 있다.

```html
<html>
  <head>
    <title>내 웹사이트</title>
  </head>
  <body>
    <p>자식문서에 포함될 내용</p>
  </body>
</html>
```

Flask 템플릿 상속 쉽다.

이제 지금까지 학습한 내용을 한 방에 예시로 익혀보자.

## 코드 예시 – 종합 요약

Flask 템플릿 내에서 변수 필터링 및 조건문/반복문, 템플릿 상속을 테스트하기 위한 간단한 예시.

학생들의 정보와 시험 성적을 딕셔너리로 만들어놓은 후, 메인 페이지에서 이름을 확인하면 해당 학생의 성적이 리스트로 출력되는 페이지를 만들어보았다.

### app.py

```python
from flask import Flask, render_template

app = Flask(__name__)

student_data = {
    1: {"name": "슈퍼맨", "score": {"국어": 90, "수학": 65}},
    2: {"name": "배트맨", "score": {"국어": 75, "영어": 80, "수학": 75}}
}

@app.route('/')
def index():
    return render_template("index.html", 
            template_students = student_data)

@app.route("/student/<int:id>")
def student(id):
    return render_template("student.html", 
            template_name=student_data[id]["name"], 
            template_score=student_data[id]["score"])

if __name__ == '__main__':
    app.run(debug=True)
```

### base.html

{% raw %}
```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>시험성적 확인 사이트</title>
</head>
<body>
    <div>
        <a href="/">메인화면 바로가기</a>
    </div>
    {% block content %}{% endblock %}
</body>
</html>
```
{% endraw %}

### index.html

{% raw %}
```html
{% extends "base.html"  %}
{% block content %}
    <h1>시험 성적 확인하기</h1>
    <p>이름을 클릭하세요.</p>
    <ul>
        {% for key, value in template_students | dictsort %}
            <li><a href="/student/{{ key }}">{{ value["name"] }}</a></li>
        {% endfor%}
    </ul>
{% endblock %}

student.html

{% extends "base.html"  %}
{% block content %}
    <h2>{{ template_name }} 님의 성적</h2>
    <ul>
        {% for key, value in template_score.items() %}
            <li>{{ key }} : {{ value }}</li>
        {% endfor%}
    </ul>
{% endblock %}
```
{% endraw %}

일단 Flask 템플릿 사용법은 여기까지. 