---
title: 로컬 PC로 웹훅 받아서 파이썬 스크립트 실행하기 (Flask)
date: 2020-08-14
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

요즘 이런저런 API 서비스를 사용하다 보면 [웹훅(webhook)](https://en.wikipedia.org/wiki/Webhook)이라는 걸 볼 수 있다. 특정 이벤트가 발생했을 때(trigger) 지정된 URL로 HTTP 요청을 보내주는 거다. 이렇게 되면 웹훅을 보내준 그 녀석이 클라이언트, 그 웹훅을 받는 곳이 서버가 되는 셈이다.

사실 상용 서비스들끼리 연결해서 이벤트가 발생하면 특정 작업을 해주는 서비스들은 꽤 있다. 예를 들면 설문 응답이 들어왔을 때 이걸 slack이나 이메일로 알림을 받도록 연동하기. 이런 서비스들의 통합 자동화는 Zapier 같은 훌륭한 서비스를 사용하면 금방 해결이 되긴 한다.

이런 서비스들이 있음에도 불구하고 내가 이 웹훅에 왜 관심을 가졌냐 하면… 내가 대응해야 하는 요청이 발생하면 그 데이터를 가지고 로컬 PC에서 작업을 해서 거의 실시간으로 대응을 하고 싶었기 때문이다. 결국 내 PC가 웹훅을 받고, 그로 인해 자동화 스크립트가 돌아가야 한다.

그래서 일단 이렇게 두 단계로 작전을 세웠다.

1. 내 PC를 서버로 동작하게 만들어 놓고 웹에 공개한다. (웹훅을 받을 URL을 준비한다.)
2. 내 PC를 가리키는 URL로 웹훅이 날아오면 내 PC에서 자동화 스크립트가 실행된다.

사실 내가 개발자도 아니고, 이런 건 깊이 들어가면 끝이 없기 때문에 최대한 심플하게 이걸 구현할 수 있는 방법을 생각해야 했다. 대단한 웹 서비스를 만드는 게 아니다.

## 1. 내 PC를 서버로 동작하게 만들어 놓고 웹에 공개한다. (웹훅을 받을 url을 준비한다.)

일단 첫 번째 단계는 [ngrok](https://ngrok.com/)이라는 무료 서비스로 아주 가볍게 해결했다.

## 2. 내 PC를 가리키는 URL로 웹훅이 날아오면 내 PC에서 자동화 스크립트가 실행된다.

두 번째 단계는 첫 단계에서 준비된 URL로 웹훅이 들어오면 그 HTTP 요청 값을 가지고 내 PC에서 자동화 스크립트가 실행되도록 하는 것. 이런저런 방법을 찾아보다가 결국 서버처럼 대기하다가 요청이 들어오면 최소한의 응답을 하도록 파이썬 Flask를 사용하게 되었다. (사실 내가 할 줄 아는 게 파이썬 밖에 없기도 하고.)

흔히 파이썬 웹 프레임워크로 [Django](https://www.djangoproject.com/)(장고) 아니면 [Flask](https://flask.palletsprojects.com/en/1.1.x/)(플라스크)를 많이 사용하는 것 같다. Django는 이런저런 백엔드에서 필요한 다양한 기능을 빵빵하게 지원하고 풀 스택 웹 프레임워크인 반면, Flask는 정말 최소한의 수준으로만 구현이 되어있는 마이크로 프레임워크라고 한다. 나 같은 초보 입장에서는 일단 최대한 가볍고 쉬운 Flask를 택할 수밖에 없었다.

일단 내가 짜놓은 코드는 아래와 같다.

```python
from flask import Flask, request, Response
import threading
from queue import Queue
from auto import auto	# 자동화 함수

app = Flask(__name__)
webhook_queue = Queue()

"""웹페이지 렌더링"""
@app.route('/')
def hello_world():
    print('Success!')
    return 'Hello World!'

"""웹훅 응답"""
@app.route('/webhook', methods=['GET', 'POST'])
def webhook():
    if request.method == 'GET':
        response = request.args.to_dict()
    elif request.method == 'POST':
        if request.is_json is True:	# response-Type: json
            response = request.get_json()
        else:	# response-Type: x-www-form-urlencoded
            response = request.form.to_dict()
			
    # 큐에 웹훅으로 받은 데이터 추가
    webhook_queue.put(response)
    
    return f'Hello World!'

# 큐에서 데이터 가져와 자동화 작업을 수행하는 함수
def process_webhooks():
    while True:
        response = webhook_queue.get()
        auto(response)	# 자동화 함수
        webhook_queue.task_done()

if __name__ == '__main__':
    # 스레드 시작
    webhook_thread = threading.Thread(target=process_webhooks)
    # 앱 종료 시 스레드 종료
    webhook_thread.daemon = True
    webhook_thread.start()
    app.run(port=80, debug=True)
```

설명 간단히 덧붙이자면, 일단 첫 번째 단계 ngrok에서 받아놓은 URL로 요청이 들어오면 ‘Hello World!’라고만 응답해준다. (실제로 해당 URL로 접속하면 헬로월드만 덩그러니 써있다.)

만약 URL 뒤에 `/webhook`이라고 붙여서 GET 메서드로 뭐가 딸려 오거나, POST 메서드로 json이나 다른 content-type의 값들이 들어올 경우 그걸 `response`라는 변수에 담아서 queue에 추가한다. (요청에 대해서는 역시나 ‘Hello World!’로만 응답하고 땡이다.)

그리고 서버는 파이썬의 `threading` 모듈을 활용해서 queue에 추가된 데이터를 가져와 자동화 함수를 실행하는데, 요청이 여러번 들어와도 작업이 겹치지 않고 스레딩으로 잘 실행되는 것까지 일단 확인했다.

### (참고) 나의 눈물겨운 삽질

자동화 함수를 끝까지 실행하는 데에 시간이 오래 걸릴 경우, 처리 중에 다른 요청이 들어오면 어쩌지 하는 걱정이 있었는데 스레딩으로 처리하니까 잘 되더라. 원래 작전은 자동화 작업이 오래 걸려서 응답만 한 후에 처리하는 방식으로 해보려고 Flask의 `after_request` 핸들러 같은 걸 따라해보려고 했는데, 오히려 정말 삽질이었다. 그냥 스레딩으로 끝나는 문제였다.

[https://stackoverflow.com/questions/48994440/execute-a-function-after-flask-returns-response](https://stackoverflow.com/questions/48994440/execute-a-function-after-flask-returns-response)

그리고 처음에 스레딩을 안 했을 땐 웹훅으로 HTTP 요청이 있을 때마다 내가 짜놓은 함수가 두 번씩 실행되더라. 이유를 도저히 알 수 없었는데, Flask의 고질적인 현상이라고 하는 것 같았다. 디버그 설정이나 재실행 관련 옵션이 원래 걸려 있기 때문에 스크립트 내에서 app.run()으로 실행할 때 debug=False, use_reloader=False 등을 넣어서 실행하면 된다는 사람도 있는 거 같고, (사실 대충 읽음)

[https://stackoverflow.com/questions/9449101/how-to-stop-flask-from-initialising-twice-in-debug-mode](https://stackoverflow.com/questions/9449101/how-to-stop-flask-from-initialising-twice-in-debug-mode)

스크립트를 실행할 게 아니라 커맨드 창에서 환경변수로 설정한 후에 실행할 때 –no-reload, –no-debugger 등 옵션을 넣어서 바로 flask를 실행하라는 글도 있었는데,

[https://www.twilio.com/blog/how-run-flask-application](https://www.twilio.com/blog/how-run-flask-application)

어쨌든 파이썬 스레딩으로 결국 다 해결 되었다. 허허…

사실 프로그래밍 언어나 웹에 대한 지식이 부족한 상태에서 공식 문서도 제대로 공부 안 하고 어떻게든 꾸역꾸역 만들려다보니 너무 힘들었다. 이게 제대로 된 학습 방식은 아닐 텐데, 그래도 이번엔 일단 돌아가게 하는 데에 의의를 두었다.

아무튼 이렇게 짜놓고 나니 웹훅 들어오면 내 PC가 잘 대기하고 그거 받아서 뚝딱뚝딱 처리한다. 기특하구먼? 

아무튼 파이썬 초보의 눈물겨운 자동화 일지는 여기서 끝.