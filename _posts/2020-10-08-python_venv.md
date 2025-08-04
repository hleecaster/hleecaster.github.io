---
title: 파이썬 가상환경 venv 사용하기
date: 2020-10-08
categories: [WORK, Python]
tags: [Python]
---

파이썬을 처음에 배울 땐 별 생각 없이 pip install 로 필요한 패키지들을 그때그때 바로바로 설치했다. 내가 뭐 본업이 개발자도 아니고, 데이터 분석 할 때 파이썬을 사용하는 수준이라 라이트 유저인데 무슨 가상환경까지 써가면서 복잡하게 살겠어? 이런 순진한 생각이었던 거지.

그런데 막상 파이썬을 계속 사용하다보니 무조건 가상환경을 사용해야 한다는 걸 뼈저리게 깨닫게 됐다. 시간이 지나면서 어떤 패키지들을 설치했었는지 정리가 안되고, 결국엔 패키지 버전이 꼬이더라.

그래서 이번 기회에 나처럼 애를 먹을 파이썬 초보자들을 위해 venv의 간단한 사용법, 그리고 폴더와 패키지 관리를 어떻게 해야하는 게 좋은지 최대한 쉽게 남겨놓기로 했다. (윈도우 기준)

## 가상환경을 사용하는 이유

당연히 파이썬 패키지(라이브러리) 간 의존성 때문에 쓰는 거다. 특정 패키지 버전을 업데이트 할 때 다른 것들이 호환되지 않아 문제가 생기는 경우가 발생한다.

그리고 작업환경이 바뀌더라도, 예를 들면 PC를 교체하더라도 필요한 패키지들을 동일한 버전으로 설치해 작업할 수 있기 때문에 무조건 사용하는 게 좋다.

### virtualenv 와 venv 중 무엇을 써야 할까

파이썬2에서는 가상환경 라이브러리가 기본적으로 제공되지 않았기 때문에 써드파티 라이브러리인 virtualenv 라이브러리를 사용하곤 했다. 그러나 파이썬3에서는 [venv](https://docs.python.org/3/library/venv.html) 라는 가상환경 라이브러리가 기본적으로 제공되기 때문에 굳이 virtualenv를 쓰지 않아도 된다.

굳이 virtualenv 설치하지 말고, 그냥 기본적으로 제공하는 venv를 쓰자.

## 가상환경 생성

예를 들어 바탕화면에 “my_project”라는 폴더를 만들어 작업을 한다면, 그 폴더 안에서 `python -m venv 가상환경이름`이라고 쳐주면 된다.

```
C:\Users\USERNAME\Desktop\my_project>python -m venv 가상환경이름
```

이러면 “my_project”라는 폴더 안에 “가상환경이름”으로 하위폴더가 하나 생성된다. 실제 작업은 프로젝트 폴더 내에서 하면 된다.

가상환경이름은 그냥 venv라고 만드는 걸 추천한다. `python -m venv venv` 이런 식으로. 어떤 프로젝트든 가상환경을 활성화 하고 싶을 때 venv라는 이름으로만 사용하면 되기 때문이다.

물론 vscode에서 파이썬 인터프리터를 선택할 때 (시스템 기본 파이썬으로 실행할지, 가상환경 파이썬으로 실행할지) 꼬일까봐 걱정할 수도 있는데, 어차피 vscode에서 프로젝트 폴더를 열면 이 녀석이 알아서 그 하위 폴더인 내 가상환경 venv에 있는 인터프리터를 인식하고 이걸로 할 거냐고 친절하게 물어봐주기 때문에 전혀 문제가 없다.

추가 팁! 애초에 가상환경을 만들 때 내가 시스템 기본 파이썬을 사용하면서 설치했던 전역 패키지들을 깔고 시작하는 방법도 있다. 명령어 칠 때 뒤에 `--system-site-packages`라고 붙여주면 된다.

```
C:\Users\USERNAME\Desktop\my_project>python -m venv 가상환경이름 --system-site-packages
```

## 가상환경 활성화/비활성화

프로젝트 폴더 안에서 `가상환경이름\Scripts\activate.bat` 이라고 쳐주면 가상환경이 활성화된다.

```
C:\Users\USERNAME\Desktop\my_project>가상환경이름\Scripts\activate.bat
```

만약 venv라고 가상환경이름을 만들었다면 `venv\Scripts\activate.bat`라고 쳐주면 될 거다.

그러면 cmd 라인 맨 왼쪽에 `(가상환경이름)`이 붙어 있는 걸 볼 수 있는데, 활성화 되었다는 뜻이다. 실제로 파이썬 인터프리터가 이 폴더 안에 있는 걸로 인식이 된 건지 확인을 해보려면 `where python`이라고 쳐보자.

```
(venv) C:\Users\USERNAME\Desktop\my_project>where python
```

그러면 일반적으로 두 줄이 뜰 거다.

- C:\Users\USERNAME\Desktop\my_project\venv\Scripts\python.exe
- C:\Users\USERNAME\AppData\Local\Programs\Python\Python39\python.exe

첫번째 줄은 가상환경 내에서 파이썬 인터프리터의 위치, 두번째 줄은 시스템 기본 파이썬 인터프리터 위치.

가상환경을 비활성화 하고 싶다면 아무것도 없이 그냥 `deactivate`라고만 치면 끝이다.

## 가상환경 내에서 패키지 설치, 삭제

가상환경 내에서 패키지를 설치하려면, 가상환경을 활성화 한 상태에서 그냥 평소처럼 `pip install`로 패키지 설치를 하면 된다. (당연히 삭제는 `pip uninstall`로 하면 되고.)

```
(venv) C:\Users\USERNAME\Desktop\my_project>pip install 패키지이름
```

가상환경 활성화 상태에서 `pip freeze`라고 명령어를 쳐보면 설치된 패키지 목록들을 볼 수 있다. (가상환경을 막 생성한 상태에서는 설치된 게 없으니 당연히 아무것도 안 뜰 거다.)

어쨌든 독립된 환경을 만들었으니 이제 원하는 패키지들을 마음껏 설치해서 일단 코딩을 열심히 하자. 그러다보면 언젠가는 이 패키지들을 관리할 일이 생기게 된다.

## 패키지 목록 관리

지금까지 설치된 패키지를 확인하는 명령어로는 가상환경을 활성화 한 채 `pip freeze`와 `pip list`가 있는데 그냥 `pip freeze`만 기억하면 된다.

`pip list`는 패키지 이름과 그 버전을 깔끔하게 출력해주는데, 그 패키지 안에 자기 자신인 pip, setuptools 처럼 기본적으로 설치된 것들도 포함해서 출력해준다. 이건 애초에 우리가 관리할 패키지가 아니니 별 필요가 없다. 뭐 궁금하면 둘 다 쳐보자. (관련 링크 : [https://stackoverflow.com/questions/18966564/pip-freeze-vs-pip-list](https://stackoverflow.com/questions/18966564/pip-freeze-vs-pip-list))

어쨌든 가상환경 활성화 상태에서 `pip freeze`라고 치면 그동안 설치된 패키지 이름 뒤에 버전이 붙어서 쫙 뜬다. 그래서 만약 이걸 써먹기 위해 requirements.txt파일로 정리하고 싶다면 아래와 같이 해주면 된다.

```
(venv) C:\Users\USERNAME\Desktop\my_project>pip freeze > requirements.txt
```

그러면 프로젝트 폴더 안에 requirements.txt라는 파일이 생성된다. 나중에 다른 가상환경에 이 목록을 똑같이 설치하거나 삭제할 때 유용하다.

만약 다른 가상환경에 이 패키지들을 한 방에 설치하려면 이 텍스트 파일을 새로운 프로젝트 폴더 안에 가져와서 `pip install -r requirements.txt` 라고 써주면 된다.

```
(venv) C:\Users\USERNAME\Desktop\my_new_project>pip install -r requirements.txt
```

반대로 requirements.txt 에 있는 목록의 패키지들을 일괄 삭제하는 것도 가능하다. 별로 쓸 일이 없긴 한데, 예를 들어 시스템 파이썬에 기본적으로 깔았던 전역 패키지들을 과감히 다 싹 삭제해버리고, 무조건 가상환경으로만 작업하려고 작정했다고 해보자. 그럼 가상환경 활성화 하지 않은 상태에서 cmd 창을 열고 requirements.txt 파일이 있는 위치에서 `pip uninstall -r requirements.txt -y`라고 써주면 된다. (뒤에 -y를 안 붙이면 패키지 하나하나 삭제할 때마다 Proceed (y/n)?라고 일일이 물어보기 때문에 -y를 붙여서 한 방에 시원하게 날리자.)

## 가상환경 삭제

내가 사용했던 가상환경, 그 안에 설치된 패키지들을 한 방에 삭제하고 싶다면 그냥 과감히 venv로 생성한 가상환경 이름의 폴더를 휴지통으로 보내면 끝이다. (가상환경을 삭제하는 별도의 명령어는 없다.)

> 파이썬 가상환경 venv 사용법 끝.