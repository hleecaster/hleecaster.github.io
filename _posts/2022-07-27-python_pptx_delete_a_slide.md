---
title: 파이썬으로 파워포인트 자동화 – 슬라이드 삭제
date: 2022-07-27
categories: [WORK, 🐍 Python]
tags: [Python, PowerPoint]
---

파이썬으로 파워포인트 슬라이드 삭제하는 방법. 나중에 내가 보려고 남겨두는 코드.

python-pptx 라이브러리를 사용한다.

```python
from pptx import Presentation

prs = Presentation("template.pptx")

# 방법 1
def delete_slide(prs, index):
    id_dict = { slide.id: [i, slide.rId] for i,slide in enumerate(prs.slides._sldIdLst) }
    slide_id = prs.slides[index].slide_id
    prs.part.drop_rel(id_dict[slide_id][1])
    del prs.slides._sldIdLst[id_dict[slide_id][0]]

# 방법 2 
def delete_slide(prs, index):
        xml_slides = prs.slides._sldIdLst
        slides = list(xml_slides)
        xml_slides.remove(slides[index])


delete_slide(prs, 6)

prs.save("output.pptx")
```

방법 1에서 id_dict 내용을 출력해보면 이런 식으로 꽤나 복잡한 아이디가 붙어 있는 걸 알 수 있다.

```
{3813: [0, 'rId2'], 3818: [1, 'rId3'], 3794: [2, 'rId4'], 3795: [3, 'rId5'], 3814: [4, 'rId6'], 3799: [5, 'rId7'], 3822: [6, 'rId8'], 3823: [7, 'rId9'], 3824: [8, 'rId10'], 3811: [9, 'rId11'], 3802: [10, 'rId12'], 3821: [11, 'rId13'], 3816: [12, 'rId14'], 3817: [13, 'rId15'], 3806: [14, 'rId16'], 3807: [15, 'rId17'], 3808: [16, 'rId18']}
```

그래서 방법 1은 이걸 조회한 뒤 원하는 슬라이드 번호에 해당하는 id를 인덱싱해서 삭제하는 방식이다.

방법 2가 편하다.

### 참고

- [https://github.com/scanny/python-pptx/issues/67](https://github.com/scanny/python-pptx/issues/67)
- [https://ggondae.tistory.com/46](https://ggondae.tistory.com/46)
