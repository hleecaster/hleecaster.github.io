---
title: 파이썬 GUI - tkinter 사용법 (코드 예시)
date: 2022-02-13
categories: [WORK, 🐍 Python]
tags: [Python, 업무자동화]
---

파이썬 내장 라이브러리 tkinter로 GUI 프로그램 만드는 코드 예제. 내가 나중에 다시 보려고 기초적인 수준만 작성해서 일단 기록해본다.

## tkinter 코드 예시

```python
import tkinter as tk
from tkinter.filedialog import askdirectory, askopenfilename, askopenfilenames
from tkinter import messagebox

root = tk.Tk()
root.title('파이썬 GUI 테스트')
root.minsize(400, 300)  # 최소 사이즈

'''기능 추가'''
# 기능1 : 디렉토리 선택
def select_directory():
    try:
        foldername = askdirectory(initialdir="./")
        if foldername:
            listbox1.delete(0, "end")
            listbox1.insert(0, foldername)
    except:
        messagebox.showerror("Error", "오류가 발생했습니다.")

# 기능2 : 파일 1개 선택
def select_file():
    try:
        filename = askopenfilename(initialdir="./", filetypes=(("Excel files", ".xlsx .xls"), ('All files', '*.*')))
        if filename:
            listbox2.delete(0, "end")
            listbox2.insert(0, filename)
    except:
        messagebox.showerror("Error", "오류가 발생했습니다.")

# 기능3 : 파일 여러개 선택
def select_files():
    try:
        filenames = askopenfilenames(initialdir="./", filetypes=(("Excel files", ".xlsx .xls"), ('All files', '*.*')))
        if filenames:
            for filename in filenames:
                listbox3.insert(0, filename)
    except:
        messagebox.showerror("Error", "오류가 발생했습니다.")
        listbox3.delete(0, "end")

# 기능4: 초기화
def refresh():
    try:
        reply = messagebox.askyesno("초기화", "정말로 초기화 하시겠습니까?")
        if reply:
            listbox1.delete(0, "end")
            listbox2.delete(0, "end")
            listbox3.delete(0, "end")
            messagebox.showinfo("Success", "초기화 되었습니다.")
    except:
        messagebox.showerror("Error", "오류가 발생했습니다.")


'''1. 프레임 생성'''
# 상단 프레임 (LabelFrame)
frm1 = tk.LabelFrame(root, text="준비", pady=15, padx=15)   # pad 내부
frm1.grid(row=0, column=0, pady=10, padx=10, sticky="nswe") # pad 내부
root.columnconfigure(0, weight=1)   # 프레임 (0,0)은 크기에 맞춰 늘어나도록
root.rowconfigure(0, weight=1)      
# 하단 프레임 (Frame)
frm2 = tk.Frame(root, pady=10)
frm2.grid(row=1, column=0, pady=10)

'''2. 요소 생성'''
# 레이블
lbl1 = tk.Label(frm1, text='폴더 선택')
lbl2 = tk.Label(frm1, text='파일 1개 선택')
lbl3 = tk.Label(frm1, text='파일 여러 개 선택')
# 리스트박스
listbox1 = tk.Listbox(frm1, width=40, height=1)
listbox2 = tk.Listbox(frm1, width=40, height=1)
listbox3 = tk.Listbox(frm1, width=40)
# 버튼
btn1 = tk.Button(frm1, text="찾아보기", width=8, command=select_directory)
btn2 = tk.Button(frm1, text="찾아보기", width=8, command=select_file) 
btn3 = tk.Button(frm1, text="추가하기", width=8, command=select_files)
btn0 = tk.Button(frm2, text="초기화", width=8, command=refresh)
# 스크롤바 - 기능 연결
scrollbar = tk.Scrollbar(frm1)
scrollbar.config(command=listbox3.yview)
listbox3.config(yscrollcommand=scrollbar.set)

'''3. 요소 배치'''
# 상단 프레임
lbl1.grid(row=0, column=0, sticky="e")
lbl2.grid(row=1, column=0, sticky="e", pady= 20)
lbl3.grid(row=2, column=0, sticky="n")
listbox1.grid(row=0, column=1, columnspan=2, sticky="we")
listbox2.grid(row=1, column=1, columnspan=2, sticky="we")
listbox3.grid(row=2, column=1, rowspan=2, sticky="wens")
scrollbar.grid(row=2, column=2, rowspan=2, sticky="wens")
btn1.grid(row=0, column=3)
btn2.grid(row=1, column=3)
btn3.grid(row=2, column=3, sticky="n")
# 상단프레임 grid (2,1)은 창 크기에 맞춰 늘어나도록
frm1.rowconfigure(2, weight=1)      
frm1.columnconfigure(1, weight=1)   
# 하단 프레임
btn0.pack()

'''실행'''
root.mainloop()
```

- 레이아웃
    - 상단은 LabelFrame으로, 하단은 Frame으로 구성했다.
    - 상단 내에는 grid로 레이아웃을 짰다.
    - 상단 LabelFrame와 그 안의 일부 grid는 창 크기에 따라 반응적으로 조정되도록 설정했다.
- 기능
    - 각 버튼을 통해 로컬 PC 안의 폴더 혹은 파일을 선택할 수 있도록 했다.
    - 버튼을 통해 선택된 폴더나 파일 이름이 Listbox 안에 삽입되도록 했다.
    - 폴더를 여러 개 선택하는 Listbox의 경우, 오른쪽에 스크롤바를 추가하여 연결했다.
    - 에러가 발생할 경우, 혹은 초기화 버튼을 클릭할 경우 messagebox 기능을 통해 알림을 띄우도록 했다.

버튼, 리스트박스, 스크롤바 등의 위젯을 활용했는데, 그 요소들을 레이아웃에 배치할 때 고생을 좀 했다. ([이 글](https://realpython.com/python-gui-tkinter/)이 도움이 많이 되었다.)
