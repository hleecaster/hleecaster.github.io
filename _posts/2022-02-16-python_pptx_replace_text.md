---
title: 파이썬으로 파워포인트 자동화 – 텍스트 찾아 바꾸기
date: 2022-02-16
categories: [WORK, Python]
tags: [Python, PowerPoint]
---

파워포인트 pptx 파일에서 특정 텍스트를 찾아서 원하는 텍스트로 바꾸는 파이썬 코드 예시.

라이브러리 python-pptx를 사용한다.

```python
from pptx import Presentation
from pptx.enum.shapes import MSO_SHAPE_TYPE

prs = Presentation("./template.pptx")

찾아바꾸기딕셔너리 = {"찾을내용" : "바꿀내용"}

for 찾을내용, 바꿀내용 in sorted(찾아바꾸기딕셔너리.items(), reverse=True):
	if type(바꿀내용) is str:
		
		# 모든 슬라이드 개체를 확인하면서 텍스트 찾아바꾸기
		for slide in prs.slides:
		    for shape in slide.shapes:
			    
				# 텍스트 상자에서
				if shape.has_text_frame:
					for paragraph in shape.text_frame.paragraphs:
						for run in paragraph.runs:
							if 찾을내용 in run.text:
								run.text = run.text.replace(찾을내용, 바꿀내용)
								
				# 그룹화된 텍스트상자에서
				if shape.shape_type == MSO_SHAPE_TYPE.GROUP:
					for shp in shape.shapes:
						if shp.has_text_frame:
							for paragraph in shp.text_frame.paragraphs:
								for run in paragraph.runs:
									if 찾을내용 in run.text:
										run.text = run.text.replace(찾을내용, 바꿀내용)
				# 표에서
				if shape.has_table:
					for row in shape.table.rows:
						for cell in row.cells:
							for paragraph in cell.text_frame.paragraphs:
								for run in paragraph.runs:
									if 찾을내용 in run.text:
										run.text = run.text.replace(찾을내용, 바꿀내용)

prs.save('./output.pptx')
```

내가 나중에 보려고 올려두는 코드.
