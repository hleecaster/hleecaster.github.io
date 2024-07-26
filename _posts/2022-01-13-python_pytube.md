---
title: 파이썬으로 사운드 재생하기
date: 2022-01-13
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

파이썬 라이브러리 중 pytube라는 걸 사용하면 유튜브에 있는 영상 관련 메타데이터를 가져오거나 영상을 동영상 파일 형식으로 다운 받을 수 있다.

일단 pytube를 설치하자. (참고: [홈페이지](https://pytube.io/en/latest/), [깃허브](https://github.com/pytube/pytube))

```
pip install pytube
```

이제 다운로드 폴더와 유튜브 영상 URL을 넣고 다운로드 해보자.

```python
from pytube import YouTube

DOWNLOAD_FOLDER = "C:\\Users\\USERNAME\\Desktop"

url = "https://www.youtube.com/watch?v=d6LGnVCL1_A"

yt = YouTube(url)

stream = yt.streams.get_highest_resolution()
stream.download(DOWNLOAD_FOLDER)
```

영상의 제목, 길이, 게시자, 날짜, 조회수, 키워드, 설명, 썸네일 URL 같은 정보도 가져올 수 있다.

```python
from pytube import YouTube

DOWNLOAD_FOLDER = "C:\\Users\\USERNAME\\Desktop"

url = "https://www.youtube.com/watch?v=d6LGnVCL1_A"

yt = YouTube(url)

print("제목 : ", yt.title)
print("길이 : ", yt.length)
print("게시자 : ", yt.author)
print("게시날짜 : ", yt.publish_date)
print("조회수 : ", yt.views)
print("키워드 : ", yt.keywords)
print("설명 : ", yt.description)
print("썸네일 : ", yt.thumbnail_url)
```

심지어 플레이리스트에 있는 영상을 모두 저장하려면 이런 식으로 반복문을 돌려줄 수도 있다.

```python
from pytube import Playlist

DOWNLOAD_FOLDER = "C:\\Users\\USERNAME\\Desktop"

p = Playlist('https://www.youtube.com/watch?v=ea7hkgs9DJo&list=PLsBObkGU9zQMirwoCV2lgHOl5fqXIlcxI')

for video in p.videos:
    video.streams.first().download(DOWNLOAD_FOLDER)
```

코드가 10줄이 채 안 된다. 역시 파이썬이 깡패다.