---
title: 파이썬으로 파워포인트 자동화 – 개체 배경, 글꼴 색상 바꾸기
date: 2022-02-18
categories: [WORK, 🐍 Python]
tags: [Python, PowerPoint]
---

파워포인트 ppt 파일에서 특정한 텍스트박스나 표의 셀을 지정해 배경이나 글꼴 색을 바꿔주는 파이썬 코드 예시.

라이브러리 python-pptx를 사용한다.

```python
from pptx import Presentation
from pptx.dml.color import RGBColor


prs = Presentation("./template.pptx")


'''색칠하기 함수 (텍스트박스, 표 모두 가능)'''
def color(shape, bg_color=None, font_color=None):
    if bg_color:
        shape.fill.fore_color.rgb = bg_color
    if font_color:
        for paragraph in shape.text_frame.paragraphs:
            for run in paragraph.runs:
                run.font.color.rgb = font_color

"""색상 팔레트"""
green = RGBColor(0, 176, 159)
yellow = RGBColor(253, 205, 1)
red = RGBColor(164, 40, 52)
gray = RGBColor(64, 64, 64)
white = RGBColor(255, 255, 255)



# 전체 슬라이드에 있는 개체들의 이름을 확인해서
for slide in prs.slides:
	for shape in slide.shapes:
		
		# 개체 이름이 "텍스트상자"인 텍스트 상자 색칠하기
		if shape.name == "텍스트상자":
			color(shape, bg_color=green, font_color=white)
		
		# 개체 이름이 "표1"인 경우,
		# 각 셀(행/열) 확인해서 "-" 문자열이 있을 경우 색칠하기   
		if shape.name == "표1":
			for col in range(len(shape.table.columns)):
				for row in range(len(shape.table.rows)):
					if "-" in shape.table.cell(row, col).text:
						color(shape.table.cell(row, col), bg_color=None, font_color=red)

prs.save('./output.pptx')
```
