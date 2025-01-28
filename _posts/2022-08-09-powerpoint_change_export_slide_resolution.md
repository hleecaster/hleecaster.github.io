---
title: 파워포인트 슬라이드 고화질 이미지로 저장/내보내기
date: 2022-08-09
categories: [WORK, MS Office]
tags: [PowerPoint]
---

일하다 보면 파워포인트 슬라이드를 이미지로 저장할 일이 있다. “다른 이름으로 저장”이나 “내보내기”를 사용하면 슬라이드를 다음과 같은 이미지 파일 형식으로 저장이 가능한데,

- JPEG 파일 교환 형식(`*.jpg`)
- PNG 형식(`*.png`)

해보면 알겠지만, 해상도가 너무 구려서 흐릿흐릿하다. 검색을 해보니 슬라이드를 이미지 형식으로 내보낼 때 기본 dpi 값이 96이더라. 요즘 같은 고화질 시대에 굳이 이렇게 낮은 dpi를 기본값으로 설정한 이유를 모르겠다. 

>참고로 dpi는 인치 당 도트 수(Dots per inch)로, 1 제곱인치 내에 포함되는 점의 개수를 말한다. 당연히 이 수치가 높을수록 화질이 선명해진다.

마이크로소프트 공식 문서 [PowerPoint에서 고해상도(높은 dpi) 슬라이드를 내보내는 방법](https://docs.microsoft.com/ko-kr/office/troubleshoot/powerpoint/change-export-slide-resolution)에서는 레지스트리 변경을 통해 dpi 수치를 변경할 수 있다고 안내하고 있다.

### 1. 레지스트리 편집기 실행

레지스트리 편집기 앱을 찾아서 열어주자. (윈도우+r 키를 눌러서 실행을 열고 `regedit` 라고 입력해도 된다.)

### 2. 레지스트리 검색 (파워포인트 버전에 따른 경로)

사용 중인 PowerPoint 버전에 따라 레지스트리 경로를 찾는다. PowerPoint 2016, 2019, Microsoft 365의 경우 다음과 같다.  
(버전에 따라 Office 뒤 하위 경로의 숫자만 다르다.)

```
HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\PowerPoint\Options
```

### 3. Options 내에 ExportBitmapResolution 추가

Options 경로를 선택한 후, “편집 → 새로 만들기 → DWORD(32비트) 값“을 선택해주면, 우측에 “새 값 #1″이라는 값이 새 파일 생성되듯이 뜨는데, 이 이름을 `ExportBitmapResolution` 라고 지정해주자.

### 4. ExportBitmapResolution 값 수정

이제 `ExportBitmapResolution`을 선택 후 우클릭하여 “수정”을 하자.

“단위”를 10진수를 선택해놓고, “값 데이터”에 300을 넣어주면 된다. 이제 300 dpi로 저장한다는 뜻.
