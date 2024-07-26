---
title: 파이썬으로 파워포인트 자동화 – 차트 데이터 업데이트
date: 2022-02-17
categories: [WORK, PowerPoint]
tags: [Python, PowerPoint]
---

파워포인트로 미리 보고서 템플릿을 작성한 후, 이 안에 있는 차트 데이터만 업데이트 하는 방법을 파이썬으로 구현했다.

stackoverflow에 이에 대한 [질문](https://stackoverflow.com/questions/54991741/python-pptx-update-or-change-existing-chart-data)이 올라왔는데, 파이썬으로 파워포인트를 다루는 라이브러리 [python-pptx](https://python-pptx.readthedocs.io/en/latest/)의 개발자인 Scanny가 직접 답변을 달았더라. 이걸 읽고 참고했다.

>참고로 python-pptx에서는 차트를 크게 둘로 구분한다. 카테고리 차트와 XY차트. (차트에 데이터를 넣을 때 항목과 계열이 구분되는 막대, 방사형 등 대부분의 차트가 카테고리 차트에 해당한다. 반면 Scatter Plot 같은 분산형 차트는 XY 차트에 해당한다.)

PPT 파일에는 다양한 개체가 있기 때문에, 내가 원하는 차트를 찾아서 지정하는 게 굉장히 번거로운 방법이다. (예를 들어, 슬라이드 번호와 그 안에서 해당 차트의 개체 번호를 확인해서 직접 지정해주는 건 쉽지 않다. 미리 다 확인해두어야 한다.)

그래서 이 작업을 하기 전에 미리 PPT 템플릿 파일 개체 선택 창으로 들어가서 각 차트의 "이름"을 지정해두는 게 좋다. 그러면 전체 슬라이드 개체를 하나씩 확인하면서 그 이름으로 차트를 찾아 데이터를 업데이트 하는 방법을 쓸 수 있다.

```python
from pptx import Presentation
from pptx.chart.data import CategoryChartData, XyChartData

prs = Presentation("./template.pptx")

# 전체 슬라이드에 있는 개체들의 이름을 확인해서
for slide in prs.slides:
	for shape in slide.shapes:
		
		# 개체 이름이 "막대형차트"일 경우 데이터 업데이트
		if shape.name == "막대형차트":
	        chart_data = CategoryChartData()
	        chart_data.categories = ["항목1", "항목2", "항목3"]
	        chart_data.add_series("계열1", (100, 200, 300))
	        chart_data.add_series("계열2", (150, 220, 275))
	        shape.chart.replace_data(chart_data)
		
		# 개체 이름이 "분산형차트"일 경우 데이터 업데이트
		if shape.name == "분산형차트":
	        chart_data = XyChartData()
	        series = chart_data.add_series("계열 1")
	        series.add_data_point(1, 2)
	        series.add_data_point(2, 4)
	        series.add_data_point(3, 6)
	        series.add_data_point(4, 8)
	        shape.chart.replace_data(chart_data)
        
prs.save('./output.pptx')
```
