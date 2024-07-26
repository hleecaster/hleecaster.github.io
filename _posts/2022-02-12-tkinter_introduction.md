---
title: 파이썬 GUI - tkinter 사용법 (기초)
date: 2022-02-12
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

파이썬으로 GUI 프로그램 만들 때 사용하는 대표적인 라이브러리로 tkinter, PyQt5 등이 있는데, 일단 간단한 수준에서 기능만 구현하기 위해서 가장 먼저 시도해볼만한 건 역시 tkinter다. 파이썬 설치하면 기본으로 딸려오는 내장 라이브러리이기 때문에.

GUI 프로그램 만들 때 레이아웃 잘 짜고 그럴싸한 디자인까지 하려면 꽤나 어렵다. 특히 UI 디자인은 거의 전문적인 영역이기 때문에. 일단 디자인은 나중에 생각하고 아주 단순한 수준에서 원하는 기능을 수행해줄 수 있는 정도까지만 구현해보자.

## tkinter 창 만들기

일단 묻지도 따지지도 않고 이렇게 쓰고 시작한다.

```python
import tkinter as tk

root = tk.Tk()

root.mainloop()
```

창이 하나 뜬 걸 알 수 있다. 이제 `root = tk.Tk()`와 `root.mainloop()` 사이에 원하는 위젯을 생성하고 그걸 잘 배치하는 게 앞으로 할 일.

## 위젯 생성

tkinter로 생성할 수 있는 위젯들은 종류가 상당히 많은데, 종류를 한 번 싹 다 훑어보고 싶다면 기본적인 것들은 [Basic Widgets](https://tkdocs.com/tutorial/widgets.html), 그 외의 추가적인 것들은 [More Widgets](https://tkdocs.com/tutorial/morewidgets.html)를 참고하자.

이번 예시에서는 일단 4개만 만들어서 넣어본다.

1. Label (텍스트 또는 이미지)
2. Button
3. Text (편집이 가능한 텍스트 박스)
4. Listbox

아래 코드를 실행해보자.

```python
import tkinter as tk

root = tk.Tk()

# Label
widget1 = tk.Label(root, text="이것은 Label입니다.")
widget1.pack()

# Button
widget2 = tk.Button(root, text="이것은 Button입니다.")
widget2.pack()

# Text
widget3 = tk.Text(root)
widget3.pack()

# Listbox
widget4 = tk.Listbox(root)
widget4.pack()

root.mainloop()
```

위에서부터 하나씩 차곡차곡 창에 배치되어 나타나는 걸 볼 수 있다. 각 위젯을 생성하고 나서는 `.pack()`을 적어줬는데, 이게 순서대로 하나씩 추가하는 방법이다. 이외에도 위젯을 배치하는 또 다른 방법으로는 정확하게 x, y 좌표를 지정하는 `.place()`, 그리고 그리드를 나눠 행과 열 번호를 지정하는 `.grid()`가 있다.

일단 지금은 각 위젯별 옵션들부터 살펴보자.

### 1. Label

레이블로 텍스트를 넣을 때 이런 저런 옵션을 지정해줄 수도 있다. 레이블로 이미지를 넣는다면 PIL 라이브러리를 사용해주어야 한다.

```python
import tkinter as tk
from PIL import ImageTk

root = tk.Tk()

# 텍스트 레이블
widget1 = tk.Label(
    root,
    text="이것은 Label입니다.",
    fg="white",
    bg="#34A2FE",
    width=40,
    height=5
)
widget1.pack()

# 이미지 레이블
img = ImageTk.PhotoImage(file="a.jpg")
widget2 = tk.Label(root, image=img)
widget2.pack()

root.mainloop()
```

### 2. Button

버튼을 넣는 건 기능을 추가하기 위함이니, 그 기능을 함수를 만들어서 버튼을 클릭할 때 실행되도록 해보자.

일단 함수를 먼저 만들어야 하는데, 예시로 `askopenfilename()`를 사용해서 파일을 선택하는 기능을 포함해보았다. 이때 파일 유형을 지정하는 것도 가능하고.

그리고 버튼 위젯 생성시 파라미터 `command`에 이 함수를 넣어주면 버튼을 클릭할 때 이 함수가 실행된다.

기왕 버튼 위젯 만드는 김에 이런저런 옵션도 넣어봤다.

```python
import tkinter as tk
from tkinter.filedialog import askopenfilename

root = tk.Tk()

# 함수 생성
def open_file():
    filename = askopenfilename(filetypes=(("Excel files", ".xlsx .xls"), ('All files', '*.*')))
    if filename:
        print(filename)

# 버튼에 함수 연결
widget = tk.Button(
    root, 
    text="파일 선택", 
    command=open_file,
    width=20,
    height=2,
    bg="blue",
    fg="yellow",
)
widget.pack()

root.mainloop()
```

만약 버튼의 텍스트를 변수로 지정해 필요에 따라 수정하고 싶다면 `tk.StringVar()`을 통해 문자열 변수를 생성한 후 버튼 생성 시 파라미터로 `textvariable`에 이 변수를 추가해주면 된다.

미묘하게 변화된 코드를 살펴보자.

```python
import tkinter as tk
from tkinter.filedialog import askopenfilename

root = tk.Tk()

# 함수 생성
def open_file():
    filename = askopenfilename(filetypes=(("Excel files", ".xlsx .xls"), ('All files', '*.*')))
    if filename:
        print(filename)
        btn_text.set("선택 완료")

btn_text = tk.StringVar()
btn_text.set("파일 선택")

# 버튼에 함수 연결
widget = tk.Button(
    root, 
    textvariable=btn_text, 
    command=open_file,
    width=20,
    height=2,
    bg="blue",
    fg="yellow",
)
widget.pack()

root.mainloop()
```

버튼의 텍스트가 변화하는 게 UI 디자인 관점에서 좋은 건 아닐 텐데, `tk.StringVar()`를 설명하기 위해 만든 예시이니 참고만 하자.

### 3. Text

Text는 창을 띄운 사용자가 직접 그 상태에서 수정도 가능한, 거의 메모장 수준의 텍스트 박스다.

기본 Text 위젯은 스크롤바가 없는데, 만약 스크롤바가 있는 텍스트박스를 넣고 싶다면 `ScrolledText`라는 걸 사용해야 한다. 해당 모듈을 불러와서 사용해야 하는 게 번거롭고 세로 스크롤만 지원하는 게 아쉽긴 하지만 그 외의 사용법은 Text와 거의 동일하기 때문에 유용하다. (이후 예시에서는 그냥 Text만 사용하겠다.)

```python
import tkinter as tk
from tkinter.scrolledtext import ScrolledText

root = tk.Tk()

# Text
widget1 = tk.Text(root, width=50, height=10)
widget1.pack()
# ScrolledText
widget2 = ScrolledText(root, width=50, height=10)
widget2.pack()

root.mainloop()
```

일단 텍스트를 삽입해보자. 텍스트를 삽입할 때 `insert()`를 사용하는데, 첫 번째 파라미터로는 시작하는 지점,(커서 위치)를 적고, 이어서 삽입할 텍스트를 적어주면 된다.

아래 코드를 실행해보자.

```python
import tkinter as tk

root = tk.Tk()

# 1.0 : 맨 앞
widget1 = tk.Text(root, width=50, height=10)
widget1.insert(1.0, "첫 번째 문장입니다.\n")
widget1.insert(1.0, "두 번째 문장입니다.\n")
widget1.insert(1.0, "세 번째 문장입니다.\n")
widget1.pack()

# "end" : 맨 뒤
widget2 = tk.Text(root, width=50, height=10)
widget2.insert("end", "첫 번째 문장입니다.\n")
widget2.insert("end", "두 번째 문장입니다.\n")
widget2.insert("end", "세 번째 문장입니다.\n")
widget2.pack()

# x.y : x번째 줄 y번째 글자
widget3 = tk.Text(root, width=50, height=10)
widget3.insert("end", "----------\n")
widget3.insert("end", "----------\n")
widget3.insert("end", "----------\n")
widget3.insert(2.7, "O")
widget3.pack()

root.mainloop()
```

커서 위치로 흔히 쓰는 건 시작 지점 `1.0` 혹은 끝 지점 `"end"`일 거다. `x.y`가 x번째 줄 y번째 글자라는 걸 이해하면 시작 지점을 1.0이라고 적어주는 이유가 이해가 된다.

만약 지우고 싶다면 `delete()`를 사용하면 되는데, 이 때 첫 파라미터로 커서 시작 위치와 지우기를 마치는 위치를 적어줘야 한다. 싹 비우고 싶으면 `widget1.delete(1.0, "end")`라고 쓰면 끝.

그럼 이제 창에서 직접 텍스트를 작성하고 버튼을 통해 삭제하는 연습을 해보자.

```python
import tkinter as tk

root = tk.Tk()

# 텍스트
widget1 = tk.Text(root, width=50, height=10)
widget1.pack()

# 버튼 (command에 lambda 함수 작성)
widget2 = tk.Button(root, text="삭제하기", command=lambda : widget1.delete(1.0, "end"))
widget2.pack()

root.mainloop()
```

한 줄을 위해 굳이 def 함수를 작성할 필요는 없으니까 람다 함수를 적용해보았다.

그리고 텍스트 위젯은 이렇게 텍스트의 삽입 혹은 수정을 허용하는 게 기본값이기 때문에 이걸 못하게 `.config()`로 속성을 지정할 수 있다. 기본값 `state='normal'`을 `state='disabled'`로 설정하면 수정이 불가하다.

```python
import tkinter as tk

root = tk.Tk()

widget = tk.Text(root, width=50, height=10)

widget.config(state='normal')
widget.insert(1.0, "삭제할 수 없는 텍스트입니다.\n")
widget.config(state='disabled')

widget.pack()

root.mainloop()
```

### 4. Listbox

Listbox는 말 그대로 리스트를 담아주는 박스.

일단 기본 사용법은 이런 형태.

```python
import tkinter as tk

root = tk.Tk()

widget = tk.Listbox(root, width=50, height=10)
for i in range(1, 6):
    widget.insert(0, f'{i}번')

widget.pack()

root.mainloop()
```

`.insert()`는 Text에서 사용하는 것과 유사하다. 다만 인덱스를 `0`으로 적은 것은 파이썬 리스트가 0부터 시작하기 때문이다. 맨 앞에 추가해주는 방식. 원하는 위치를 정수로 적어주면 리스트 내 해당 위치에 요소를 삽입한다. 만약 맨 뒤에 넣고 싶다면 똑같이 `"end"`로 적어주면 된다.

그리고 리스트 박스는 선택한 목록을 가져와 처리한다든가 하는 작업도 가능하게 해주는데, 너무 복잡해지니 본 포스팅에서 자세한 설명은 생략한다.

리스트박스는 Text처럼 스크롤바를 기본으로 장착할 수 없기 때문에 스크롤바 위젯을 별도로 만들고 기능을 상호 연결해야 한다. 그리고 붙여서 배치해주면 된다.

```python
import tkinter as tk

root = tk.Tk()

# 위젯 생성
widget = tk.Listbox(root, width=50)
for i in range(100):
    widget.insert("end", i)
scrollbar = tk.Scrollbar(root)

# 기능 상호 연결
widget.config(yscrollcommand=scrollbar.set)
scrollbar.config(command=widget.yview)

# 배치
widget.pack(side="left", fill="y")
scrollbar.pack(side="right", fill="y")

root.mainloop()
```

좌우로 붙여서 배치할 때 `.pack()`에서 `side`와 `fill` 파라미터를 사용했다.

사실 요소들이 많아지면 정교한 배치를 위해 `.grid()`를 사용하는 게 좋은데, 레이아웃 관련한 내용은 다음 포스팅에 따로 소개하는 게 좋겠다.

## 메시지 박스 띄우기

프로그램 실행하다 보면 알림 창, 팝업 같은 게 뜰 때가 있다. tkinter에서는 messagebox를 통해 그 기능을 제공한다.

일단 하나만 테스트로 해보자. 버튼 누르면 알림 뜨도록 설정.

```python
import tkinter as tk
from tkinter import messagebox

root = tk.Tk()

widget = tk.Button(root, text="테스트", command=lambda : messagebox.showinfo())
widget.pack()

root.mainloop()
```

`messagebox.showinfo()`라는 걸 사용해서 빈 알림 창을 띄웠다.

괄호 안에는 title=”제목”, message=”내용”을 넣어줄 수 있다. (파라미터 생략하려면 문자열 2개를 순서대로 넣어주면 된다.)

그리고 당연히 메시지 박스의 종류도 상당히 다양하다. 아래 코드를 실행해서 한 번씩 구경해보자.

```python
import tkinter as tk
from tkinter import messagebox

root = tk.Tk()

messagebox.askokcancel(message="askokcancel()")
messagebox.askquestion(message="askquestion()")
messagebox.askretrycancel(message="askretrycancel()")
messagebox.askyesno(message="askyesno()")
messagebox.askyesnocancel(message="askyesnocancel()")
messagebox.showerror(message="showerror()")
messagebox.showinfo(message="showinfo()")
messagebox.showwarning(message="showwarning()")

root.mainloop()
```

확인/취소, 예/아니오를 구분할 뿐만 아니라 다시 시도 같은 옵션도 있다. 사용자로부터 돌려받는 return 값들도 True, False, None 으로 구분되는 등 미묘하게 다르기 때문에 다양한 상황에서 필요에 따라 사용하면 된다.

일단 tkinter 기초는 여기까지. 

다음에 기회되면 프레임이나 그리드로 레이아웃 짜는 것도 정리해서 올려야겠다.