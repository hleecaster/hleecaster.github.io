---
title: 파이썬으로 슬랙 메시지 보내기
date: 2021-03-10
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

난 슬랙(Slack)을 개인적인 푸시 알림용으로 사용하고 있다. 사실 [Zapier](https://zapier.com/) 같은 서비스를 통해서도 특정 이벤트가 발생했을 때 알림을 받을 수 있긴 한데, 이번에는 로컬 PC에서 스크립트가 돌다가 어떤 조건이 발생할 경우 특정 채널에 메시지를 보내야 했기 때문에 간단히 코딩을 했다.

“파이썬으로 슬랙 메시지 보내는 방법”에 대해 좀 구글링 해봤더니, 대부분의 사람들이 슬랙에서 공식으로 지원하는 파이썬 라이브러리 [slack_sdk](https://slack.dev/python-slack-sdk/)를 사용하거나 오픈소스 [slacker](https://github.com/os/slacker/) 라는 라이브러리 같은 걸 사용하더라.

그러나 내가 원하는 기능은 특정 채널에 메세지를 보내는 게 전부였고, 파이썬 라이브러리를 굳이 추가로 설치하고 싶은 생각이 없었다.

그래서 [슬랙 공식 문서](https://api.slack.com/messaging/sending#publishing)를 읽어보니 HTTP 통신만으로 처리할 수 있다는 걸 알게 되었다. 그래서 이 방법으로 처리하기로.

## 준비사항

1. [Slack API](https://api.slack.com/)의 [Your Apps](https://api.slack.com/apps/) 페이지에서 App을 생성한다.
2. OAuth & Permissions 설정에서 Bot Token Scopes, 즉 봇 토큰의 사용권한으로 [chat:write](https://api.slack.com/scopes/chat:write)를 추가한다.
3. Install your app > Install to Workspace로 해당 App이 내 워크스페이스에 접근 가능하도록 연결해준다.
4. 위 설정을 마치면 “xoxb-“로 시작되는 Bot User OAuth Token이 발급되는데, 이를 별도로 보관한다.

## 파이썬 코드 예시

HTTP 통신에 필요한 최소한의 모듈 requests, json 만 사용한다.

단순히히 채널에 메시지만 보내는 함수 외에도 다른 메시지를 찾거나 (슬랙에서는 채널 내에서 타임스탬프가 메시지의 고유 ID 기능을 한다.) 답글 또는 이모지 반응하는 함수도 작성해봤다.

```python
import requests
import json

SLACK_BOT_TOKEN = "xoxb-????????????-?????????????-????????????????????????"

headers = {
    'Authorization': f'Bearer {SLACK_BOT_TOKEN}',
    'Content-type': 'application/json',
	}


"""메시지 발송하기"""
def post_message(채널이름, 텍스트):
    url = "https://slack.com/api/chat.postMessage"
    payload = {
	    "channel": 채널이름, 
	    "text": 텍스트
		}
	requests.post(url, headers=headers, json=payload)

	
"""특정 채널 타임스탬프(ts) 메시지에 댓글 달기"""
def reply_message(채널이름, 타임스탬프, 텍스트):
	url = "https://slack.com/api/chat.postMessage"
	payload = {
		"channel": 채널이름,
		"thread_ts": 타임스탬프,
		"text" : 텍스트
	}
    requests.post(url, headers=headers, json=payload)

  
"""채널에서 특정 메시지의 타임스탬프(ts) 찾아 이모지로 반응하기"""
def reply_message_by_emoji(채널ID, 타임스탬프, 이모지):
	url = "https://slack.com/api/reactions.add"
	payload = {
	    "channel": 채널ID,   # NOT 채널명
	    "timestamp": 타임스탬프,
	    "name" : 이모지
        }
    requests.post(url, headers=headers, json=payload)


"""특정 채널 최근 N개 메시지 중 조건을 만족하는 메시지의 타임스탬프(ts) 찾기"""
# requests.post, 'application/x-www-form-urlencoded'를 사용해야 함
def search_message(채널ID, 검색개수=100, 검색어리스트):
    headers = {
        'Authorization': f'Bearer {SLACK_BOT_TOKEN}',
        'Content-type': 'application/x-www-form-urlencoded',	# 이것만 이렇게
        }
        
    payload = {
        "channel": 채널ID,	# NOT 채널명
        "limit" : 검색개수,	# 최근 100개
        }
        
    url = f"https://slack.com/api/conversations.history?channel={payload['channel']}&limit={payload['limit']}"
    
    response = requests.post(url, headers=headers)  # GET 대신 POST로
    response_json = json.loads(response.text)

    for message in response_json['messages']:
        try:
            # 검색어리스트에 있는 모든 검색어가 메시지 블록들 중 하나 이상에 포함되는지 확인
            if all(any(str(검색어) in d['text']['text'] for d in message["blocks"]) for 검색어 in 검색어리스트):
                ts = message["ts"]
                return ts
            else:
                pass
        except:
            pass


```

끝.