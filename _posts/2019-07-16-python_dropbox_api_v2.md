---
title: 파이썬으로 드롭박스 API를 활용해 파일 업로드하기
date: 2019-07-16
categories: [WORK, 🐍 Python]
tags: [Python, 업무자동화]
---

오늘은 로컬 PC와 연동하지 않고, 곧바로 드롭박스에 파일을 업로드하는 파이썬 스크립트를 짜보았다. (경로를 한글로 설정할 때 시행착오가 있었기 때문에 나중에 보려고 기록으로 남겨놓는 글.)

참고로 드롭박스 API는 v1이 2017년 여름에 서비스를 종료하고, v2를 지원하고 있다. 그래서 본 포스팅도 v2를 기준으로 설명한다.

## 드롭박스 API 활용 준비

드롭박스 API를 활용하려면 "앱"을 만들고 access token을 받아야 한다.

아래 [드롭박스 개발자 사이트](https://www.dropbox.com/developers)에 접속해서 우측 상단에 보이는 App Console → Create app을 클릭하면 된다.

그러면 콘솔 화면에 방금 만든 앱이 생성되는 걸 확인할 수 있다. 들어가보면 Settings 탭이 있는데 여기서 Generated access token 부분만 봐도 된다. Generate 버튼을 눌러 생성된 토큰을 확인해놓자.

준비는 끝났다.

## 드롭박스 API 파이썬 활용 방법

파이썬으로 드롭박스 API를 활용하는 방법은 2가지 선택지가 있다.

1. Python SDK를 설치해서 dropbox 모듈을 불러오는 방법
2. REST API를 활용하는 방법

둘 다 다뤄보겠다. 

참고로 2번에서 한글 인코딩 문제 때문에 상당히 애를 먹었다. 그래서 본 예제도 파일명과 드롭박스 경로를 모두 한글로 소개한다.

### 1) Python SDK 설치 후 dropbox 모듈 활용

SDK를 다운받아 설치하는 방법은 단순하다. 맨날 하던 거.

```
pip install dropbox
```

파이썬 예제 스크립트는 아래와 같다. `access_token`에는 위에서 받아놓은 토큰 값을 입력해주면 된다.

```python
import dropbox
dbx = dropbox.Dropbox(access_token)

filename = "테스트파일.txt"
pathname = "/테스트폴더/테스트파일.txt"

with open(filename, "rb") as f:
    dbx.files_upload(f.read(), pathname, mode=dropbox.files.WriteMode.overwrite)
```

파일을 업로드할 경로는 루트 폴더를 기준으로 하여 슬래시(`/`)로 시작하면 된다. 단, 끝에 파일명까지 다 적어줘야 한다.

파일을 업로드 할 때는 같은 이름의 파일이 이미 존재할 때 어떻게 할지 `WriteMode`(add, overwrite, update)를 통해 결정할 수 있다. 본 예제에서는 `overwrite`로 덮어 썼다.

자세한 내용은 [공식문서](https://dropbox-sdk-python.readthedocs.io/en/latest/index.html)을 참고하자.

### 2) REST API 활용

파이썬으로 REST API를 호출할 때는 `requests`를 사용한다.

이제 위와 동일한 작동을 하는 예제 코드를 적어본다.

```python
import requests
import json

filename = "테스트파일.txt"
pathname = "/테스트폴더/테스트파일.txt"

url = "https://content.dropboxapi.com/2/files/upload"

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type": "application/octet-stream",
    "Dropbox-API-Arg": json.dumps({"path": pathname, "mode": {".tag": "overwrite"}})
    }

with open(filename, "rb") as f:
    r = requests.post(url, headers=headers, data=f.read())
```

`headers`에서 `Dropbox-API-Arg` 값을 `json.dumps`해서 넣은 걸 유의하자.

> 경로가 영어였을 때는 `{"path": pathname }`으로 쓰면 끝나는 문제였다. 근데 한글이 들어간 경로는 계속 로컬 인코딩 에러가 나더라.
> 구글링 해보니 드롭박스 깃허브에 드롭박스 개발자 Greg라는 사람이 직접 [json.dumps로 해결하라](https://github.com/dropbox/dropbox-sdk-python/issues/82#issuecomment-265575389)는 답변을 했다. 파이썬 딕셔너리, 바이너리, 유니코드, 파싱 어쩌구 할 때 발생하는 인코딩 문제 같던데... 아무튼 해결.

REST API로 더 다양한 기능을 활용하고 싶다면 이 [웹사이트](https://dropbox.github.io/dropbox-api-v2-explorer/#files_upload)를 참고하자. 몇 가지 옵션을 넣고 curl, python, HTTP 중 하나를 선택하면 아래와 같이 예제코드를 직접 작성해주기도 한다.

아무튼 위 스크립트들을 실행하면 아래와 같이 파일이 잘 올라가고, 혹은 덮어써지는 걸 확인할 수 있다. 만약 공유 폴더에 파일을 올리고 싶다면 미리 폴더 만들어 공유 설정까지 해놓은 뒤 똑같이 경로만 그쪽으로 지정해주면 된다.

