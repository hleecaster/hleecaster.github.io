---
title: 파이썬으로 파워포인트 자동화 – 슬라이드 복제/복사
date: 2022-07-21
categories: [WORK, 🐍 Python]
tags: [Python, PowerPoint]
---

파이썬으로 파워포인트 슬라이드 복제하는 방법 코드 예시. python-ppyx 라이브러리를 사용한다.

## 파워포인트 슬라이드 복제 – 파이썬 코드 예시

```python
from pptx import Presentation
import copy

'''슬라이드 복사 함수'''
def duplicate_slide(prs, index):
    
    # 원본 슬라이드 번호 정의
    source_slide = prs.slides[index]

    # 빈 레이아웃 설정 (슬라이드 레이아웃 정의 필요)
    try:
        slide_layout = prs.slide_layouts[6]
    except:
        slide_layout = prs.slide_layouts[len(prs.slide_layouts)]

    copied_slide = prs.slides.add_slide(slide_layout)

    # 원본 슬라이드에서 shape 복제
    for shape in source_slide.shapes:
        el = shape.element
        newel = copy.deepcopy(el)
        copied_slide.shapes._spTree.insert_element_before(newel, 'p:extLst')

    return copied_slide
```

슬라이드를 복제하기 전에 슬라이드 레이아웃을 정해야 하는데, 빈 레이아웃으로 해주어야 잡스러운 개체 틀이 포함되지 않는다.

Office 테마 기본 레이아웃 상에서 빈 레이아웃은 기본적으로 (파이썬에서 번호는 0부터 시작하기 때문에) 6번이다.

만약 직접 슬라이드 마스터에서 레이아웃을 설정할 수 있다면, 원하는 레이아웃을 만들고 그 번호를 넣어줘도 된다.

### 응용

나는 엑셀에서 데이터를 불러와 그 행 개수만큼 슬라이드를 복제해놓고, 해당 슬라이드에서 텍스트를 찾아 바꾸는 작업을 했기 때문에 다음과 같이 코드를 작성해서 활용했다. (openpyxl 라이브러리 활용)

```python
from openpyxl import load_workbook
from pptx import Presentation
import copy


'''슬라이드 복사 함수'''
def duplicate_slide(prs, index):
    
    # 원본 슬라이드 번호 정의
    source_slide = prs.slides[index]

    # 빈 레이아웃 설정 (슬라이드 레이아웃 정의 필요)
    try:
        slide_layout = prs.slide_layouts[0]
    except:
        slide_layout = prs.slide_layouts[len(prs.slide_layouts)]

    copied_slide = prs.slides.add_slide(slide_layout)

    # 원본 슬라이드에서 shape 복제
    for shape in source_slide.shapes:
        el = shape.element
        newel = copy.deepcopy(el)
        copied_slide.shapes._spTree.insert_element_before(newel, 'p:extLst')

    return copied_slide


'''슬라이드에서 텍스트 찾아 바꾸기 함수'''
def replace_text_in_silde(prs, index, content):
    for 찾을내용, 바꿀내용 in sorted(content.items(), reverse=True):
        for shape in prs.slides[index].shapes:
            # 텍스트박스에서
            if shape.has_text_frame:
                for paragraph in shape.text_frame.paragraphs:
                    for run in paragraph.runs:
                        if 찾을내용 in run.text:
                            run.text = run.text.replace(찾을내용, str(바꿀내용))
            # 표에서
            if shape.has_table:
                for row in shape.table.rows:
                    for cell in row.cells:
                        for paragraph in cell.text_frame.paragraphs:
                            for run in paragraph.runs:
                                if 찾을내용 in run.text:
                                    run.text = run.text.replace(찾을내용, str(바꿀내용))


'''엑셀에서 데이터 불러오기'''
wb = load_workbook("./data.xlsx", data_only=True)
ws = wb.active

contents = []
keys = [row.value for row in ws["1:1"]]
for row in ws.iter_rows(min_row=2):
    content = dict(zip(keys, [cell.value for cell in row]))
    contents.append(content)


'''파워포인트 작업'''
prs = Presentation("./template.pptx")

# 개수-1 만큼 슬라이드 복제
for number in range(len(contents)-1):
    duplicate_slide(prs, 0)

# 각 슬라이드마다 데이터 찾아바꾸기 
for index, content in enumerate(contents):
    replace_text_in_silde(prs, index, content)

# 저장
prs.save('./output.pptx')
```

### 참고

- [https://github.com/scanny/python-pptx/issues/132](https://github.com/scanny/python-pptx/issues/132)
- [https://stackoverflow.com/questions/50866634/python-pptx-copy-slide](https://stackoverflow.com/questions/50866634/python-pptx-copy-slide)
- [https://ggondae.tistory.com/45](https://ggondae.tistory.com/45)
