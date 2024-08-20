---
title: 파이썬으로 Gmail(지메일) API 사용하기
date: 2021-09-03
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

보통 이메일 자동화를 할 때 SMTP 서버를 활용한다. 이게 가장 간편하기 때문에. 그러나 SMTP 사용하면 발송할 때 제한이 있는 경우도 있어서, 이번에는 구글에서 공식적으로 제공하는 Gmail(지메일) API를 사용해서 메일을 보내는 방법을 테스트 해봤다.

[공식 문서](https://developers.google.com/gmail/api/quickstart/python)에 있는 설명 그대로 따라했는데, 크게 “프로젝트 생성 및 사용자 인증”과 “파이썬 코드 작성”으로 단계를 구분할 수 있다.

## 프로젝트 생성 및 사용자 인증 정보 생성

일단 [console.cloud.google.com](https://console.cloud.google.com/)에 접속해서 프로젝트를 생성하고, "API 및 서비스" 메뉴에서 [Gmail API](https://developers.google.com/gmail/api)를 찾아 활성화해준다. 프로젝트 내에서 해당 API 기능들을 사용하겠다고 등록하는 정도의 절차라 이해하면 된다.

그 다음 "API 및 서비스" 하위 메뉴 "사용자 인증 정보"로 들어가서 인증 정보를 만들어야 한다. 인증정보 만드는 방법은 현재 총 세 가지가 있는데,

1. API 키
2. Outh 클라이언트 ID
3. 서비스 계정

나는 여기서 Outh 클라이언트 ID를 생성했다. 본인한테 맞는 인증방법을 사용하면 된다. ID를 생성하려고 하면 애플리케이션 종류를 선택하는데, 나 혼자 테스트 용도로 사용하는 거라 ‘데스크톱 앱’을 골랐다.

클라이언트 ID를 생성하고 나면 JSON 파일을 하나 다운 받을 수 있다. 이걸 내 파이썬 스크립트가 담길 폴더 안에 `credentials.json`라고 잘 저장해두자.

이제 필요한 파이썬 라이브러리를 설치해두자.

```
pip install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib
```

이렇게 준비 끝.

## Gmail API 메일 발송 파이썬 코드 예시

사실 코드 샘플이 이미 [공식 문서](https://developers.google.com/gmail/api/guides/sending)에 나와 있는데, 이대로 돌리면 에러가 나더라. 그래서 에러 손 보면서 코드도 좀 더 심플하게 수정했다.

사용자 인증, 메시지 생성, 메시지 발송. 이렇게 세 단계로 나눠서 함수를 생성하고 이를 이어서 실행하는 구조.

그리고 첨부파일이 있을 경우, 파일명을 리스트 형식으로 담아 첨부하도록 했다.

```python
from pathlib import Path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from googleapiclient import errors
from email.message import EmailMessage
import base64


""" 1. gmail 사용자 인증 """
def gmail_authenticate():
	SCOPES = ['https://mail.google.com/']
	creds = None
	if Path('token.json').is_file():
		creds = Credentials.from_authorized_user_file('token.json', SCOPES)
	if not creds or not creds.valid:
		if creds and creds.expired and creds.refresh_token:
			creds.refresh(Request())
		else:
			flow = InstalledAppFlow.from_client_secrets_file('credentials.json', SCOPES)
			creds = flow.run_local_server(port=0)
		with open('token.json', 'w') as token:
			token.write(creds.to_json())
	return build('gmail', 'v1', credentials=creds)


"""2. 메시지 생성"""
def create_message(받는사람이메일, 제목, 본문, 첨부파일리스트=None):
	message = EmailMessage()
	message["From"] = 보내는사람이메일
	message["To"] = 받는사람이메일.split(",")
	message["Subject"] = 제목
	message.set_content(본문)
	# message.set_content(본문, subtype='html') # html 형식으로 보내려면 subtype 지정
	
	# 파일 첨부 (하위 디렉토리 files 내에 첨부파일 준비)
	if 첨부파일리스트:
		for 파일명 in 첨부파일리스트:
			파일명 = Path(파일명).name
			with open("files/"+파일명, "rb") as f:
				message.add_attachment(f.read(), maintype="application", subtype="octet-stream", filename=파일명)
		
	return {'raw': base64.urlsafe_b64encode(message.as_bytes()).decode('utf8')}


"""3. 메시지 발송"""
def send_message(service, user_id, message):
	try:
		message = service.users().messages().send(userId=user_id, body=message).execute()
		# print(message['id'])
		return message
	except errors.HttpError as error:
		print(error)


"""main"""
def main():
	service = gmail_authenticate()
	message = create_message("받는사람이메일", "제목", "본문")
	send_message(service, "me", message)
```


처음에 실행하면 웹 브라우저 창이 뜨면서 구글 계정에 로그인 한 후, 해당 애플리케이션이 내 이메일에 대한 권한을 갖도록 하겠냐고 묻는다. 이렇게 한 번 인증을 해주면 내 폴더 안에 `token.json` 라는 파일이 별도로 생겨서, 이후에는 이 토큰이 있기 때문에 권한을 주겠냐고 묻지 않는다.

어쨌든 기록으로 남기는 포스팅.