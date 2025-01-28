---
title: 로컬 PC 웹 서버 만들기 (ngrok)
date: 2020-08-05
categories: [WORK, 💻 Etc.]
tags: [업무자동화]
---

최근에 내 로컬 PC가 웹 서버 역할을 하도록 할 일이 생겼다. 추후에 외부에서 제공하는 웹훅(webhook) 서비스를 사용해서 특정 이벤트가 있을 때마다 내 PC에서 파이썬 스크립트를 실행하도록 하는 게 목표다.

localhost를 외부에서 접속 가능한 상태로 만들기. 결론은 ngrok을 쓰는 거다.

## ngrok 란

[ngrok 공식 홈페이지](https://ngrok.com/)에 보면 이렇게 써있다.

>Ngrok exposes local servers behind NATs and firewalls to the public internet over secure tunnels.

NATs와 방화벽 뒤에 있는 로컬 서버를 안전한 터널을 통해 공개 인터넷에 노출시켜 주는 도구.

즉, 내 PC에서 돌아가는 로컬 호스트를 웹 상에도 공개시켜주는 거다. ngrok를 쓰면 여기서 생성해주는 URL을 통해 외부에서도 내 PC에 있는 html 문서를 요청하거나 HTTP 요청을 하는 게 가능해진다.

## ngrok 설치 (윈도우 기준)

[https://ngrok.com/download](https://ngrok.com/download) 에서 zip 파일을 받아 압축을 풀면 ngrok.exe라는 파일이 하나 있는데, 이걸 그냥 윈도우에서 바로 실행해보면 커맨드 창이 열리면서 다음과 같은 안내가 뜬다.

```
NAME:
   ngrok - tunnel local ports to public URLs and inspect traffic

DESCRIPTION:
    ngrok exposes local networked services behinds NATs and firewalls to the
    public internet over a secure tunnel. Share local websites, build/test
    webhook consumers and self-host personal services.
    Detailed help for each command is available with 'ngrok help <command>'.
    Open http://localhost:4040 for ngrok's web interface to inspect traffic.

EXAMPLES:
    ngrok http 80                    # secure public URL for port 80 web server
    ngrok http -subdomain=baz 8080   # port 8080 available at baz.ngrok.io
    ngrok http foo.dev:80            # tunnel to host:port instead of localhost
    ngrok http https://localhost     # expose a local https server
    ngrok tcp 22                     # tunnel arbitrary TCP traffic to port 22
    ngrok tls -hostname=foo.com 443  # TLS traffic for foo.com to port 443
    ngrok start foo bar baz          # start tunnels from the configuration file

VERSION:
   2.3.35

AUTHOR:
  inconshreveable - <alan@ngrok.com>

COMMANDS:
   authtoken    save authtoken to configuration file
   credits      prints author and licensing information
   http         start an HTTP tunnel
   start        start tunnels by name from the configuration file
   tcp          start a TCP tunnel
   tls          start a TLS tunnel
   update       update ngrok to the latest version
   version      print the version string
   help         Shows a list of commands or help for one command

ngrok is a command line application, try typing 'ngrok.exe http 80'
at this terminal prompt to expose port 80.
```

이런 저런 명령어들이 있는데, 어쨌든 잘 받아진 거다.

## URL 갱신, 세션 만료 해결하기 (회원가입 후 Authtoken 설정)

`ngrok`은 기본 `Session` 유지 시간이 8시간이라서, 이후에는 다시 `ngrok`을 실행해야 한다. 그런데 무료 사용의 경우 `ngrok` 종료 후 다시 실행하면 접속 URL이 변경되는 문제가 있다.

만약 고정된 URL을 사용하고 싶다면 일단 [여기](https://dashboard.ngrok.com/user/signup) 들어가서 가입을 하자. 그리고 로그인 후에 [여기](https://dashboard.ngrok.com/auth/your-authtoken)에서 내 Authtoken을 복사해놓고 그걸 커맨드라인에 이렇게 써주어 실행하면 된다.

```
ngrok.exe authtoken "복사한토큰"
```

이제 준비 끝이다.

## ngrok 사용법

일단 커맨드라인에 `ngrok.exe http 80` 이라고 치자. (이 파일을 실행하면서 로컬호스트 80번 포트를 사용해 HTTP 터널을 시작한다는 의미다.)

해보면 ngrok이 실행되며 창이 하나 뜨는데, Forwarding이라고 제공된 URL을 실제로 외부에서 접속해보면 내가 준비한 로컬 호스트가 그대로 열리는 걸 알 수 있다.

그리고 이 URL에 접속할 때마다 GET, POST 등 어떤 요청이 있었는지 접속 로그를 보여주기 때문에 가벼운 테스트 용도로 사용하기에 좋은 것 같다.

무료 플랜에서는 분당 접속 횟수가 제한되어 있지만, 돈을 좀 더 내면 더 많이 자유롭게 쓸 수 있는 이런 저런 옵션들이 있으니 자세한 내용은 [가격 정책](https://ngrok.com/pricing)과 [공식 문서](https://ngrok.com/docs)를 살펴보자.

아무튼 나는 일단 잘 되는 걸 테스트 했으니 이제 다음 단계로 넘어가야지.

> 내 로컬 PC를 웹서버로 만들기. ngrok으로 해결 완료.