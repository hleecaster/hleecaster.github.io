---
title: 파이썬으로 사운드 재생하기
date: 2021-11-23
categories: [WORK, 🐍 Python]
tags: [Python, 업무자동화]
---

얼마 전에 작업 시간이 꽤 오래 걸리는 파이썬 자동화 스크립트를 짠 적이 있다. 기다리기 지루해서 다른 일을 좀 하고 있었는데, 실행해놓은 파이썬 코드가 완료됐는지 창을 매번 열어서 확인해보는 게 생각보다 번거롭더라.

그래서 실행이 완료되거나 에러가 발생했을 때 알림음이 재생되면 좋겠다는 생각이 들었다.

나중에 내가 다시 보려고 남기는 포스팅.

## winsound

[winsound](https://docs.python.org/ko/3/library/winsound.html)는 별도의 설치가 필요없는 파이썬 표준 라이브러리다.

주파수와 재생길이를 지정해서 알림음을 재생할 수 있으며, 따로 준비된 wav파일 재생도 가능하다. (mp3파일 재생은 안 된다.)

```python
import winsound

# 알림음 재생
winsound.Beep(
    frequency=440,  # Hz
    duration=1000  # milliseconds
    )

# wav 파일 재생
winsound.PlaySound("example.wav", winsound.SND_FILENAME)
```

## playsound

[playsound](https://github.com/TaylorSMarks/playsound) 라이브러리는 별도로 설치를 해줘야 한다.

mp3 파일 재생이 가능하다.

```python
from playsound import playsound

# mp3 파일 재생
playsound("example.mp3")
```

이제 파이썬 스크립트 돌려놓고 다른 일을 하다가 실행이 완료되면 사운드로 알림을 받을 수 있다.