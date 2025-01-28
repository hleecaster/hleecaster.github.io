---
title: 파이썬으로 나라장터 입찰공고 크롤링
date: 2019-07-12
categories: [WORK, 🐍 Python]
tags: [Python, 업무자동화]
---

회사에서 조달청 [나라장터](https://www.g2b.go.kr)에 올라오는 입찰공고를 주기적으로 확인하고 싶다는 요구가 있어서 파이썬 selenium으로 크롤링하는 코드를 직접 짜봤다.

사실 다른 블로그에서도 소스코드를 좀 확인할 수 있는데 너무 복잡한 방식으로 되어 있고 필요 없는 정보들도 너무 많이 가져오는 것처럼 보였다.

나는 필요한 정보만 소식지처럼 받아보는 것이 주된 목적이었기 때문에 최대한 가볍고 단순하게 코드를 짜는 것에 중점을 두었다.

## 나라장터 입찰공고 검색 웹페이지 확인

일단 웹페이지를 살펴보자. 입찰공고를 검색하는 웹페이지 URL은 다음과 같다.

https://www.g2b.go.kr:8101/ep/tbid/tbidFwd.do

검색어를 넣는 것뿐만 아니라 체크박스를 클릭하거나, 드롭다운 메뉴를 선택하는 등의 동작을 해야 한다. 그리고 검색 버튼을 눌렀을 때 표 형태로 공고 결과가 나오는 걸 알 수 있다.

## 나라장터 입찰공고 크롤링 파이썬 코드 예시

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By

from selenium.webdriver.chrome.service import Service



def crawl(검색어리스트):

    검색결과 = []

    service = Service()
    options = webdriver.ChromeOptions()
    driver = webdriver.Chrome(service=service, options=options)

    for query in 검색어리스트:

        # 입찰정보 검색 페이지로 이동
        driver.get('https://www.g2b.go.kr:8101/ep/tbid/tbidFwd.do')

        # 업무 종류 체크
        task_dict = {'용역': 'taskClCds5', '민간': 'taskClCds20', '기타': 'taskClCds4'}
        for task in task_dict.values():
            checkbox = driver.find_element(By.ID, task)
            checkbox.click()

        # id값이 bidNm인 태그 가져오기
        bidNm = driver.find_element(By.ID, 'bidNm')
        # 내용을 삭제
        bidNm.clear()
        # 검색어에 문자열 전달
        bidNm.send_keys(query)
        bidNm.send_keys(Keys.RETURN)

        option_dict = {'검색기간 1달': 'setMonth1_1', '입찰마감건 제외': 'exceptEnd', '검색건수 표시': 'useTotalCount'}
        for option in option_dict.values():
            checkbox = driver.find_element(By.ID, option)
            checkbox.click()

        # 목록수 100건 선택 (드롭다운)
        recordcountperpage = driver.find_element(By.NAME,'recordCountPerPage')
        selector = Select(recordcountperpage)
        selector.select_by_value('100')

        # 검색 버튼 클릭
        search_button = driver.find_element(By.CLASS_NAME, 'btn_mdl')
        search_button.click()

        # 검색 결과 확인
        elem = driver.find_element(By.CLASS_NAME,'results')
        div_list = elem.find_elements(By.TAG_NAME, 'div')

        # 표 전체 텍스트 긁어서 리스트에 담기
        공고table = []
        for div in div_list:
            공고table.append(div.text)
            a_tags = div.find_elements(By.TAG_NAME,'a')
            if a_tags:
                for a_tag in a_tags:
                    link = a_tag.get_attribute('href')
                    공고table.append(link)

        # 표전체텍스트를 행 단위로, 12개씩 나눠 새로운 공고리스트 생성
        공고list = [공고table[i:i+12] for i in range(0,len(공고table), 12)]

        # 공고리스트에서 필요한 정보만 딕셔너리로 저장하여 검색결과 전체에 추가
        for 행 in 공고list:
            공고dict = {}
            공고dict["공고번호"] = 행[1]
            공고dict["공고기관"] = 행[6]
            공고dict["공고명"] = 행[4]
            공고dict["입찰마감일시"] = 행[9].replace("\n", "  ~  ")
            공고dict["URL"] = 행[5]

            if 공고dict not in 검색결과:
                검색결과.append(공고dict)

    # 드라이버 종료
    driver.quit()

    return 검색결과


검색결과 = crawl(['검색어1', '검색어2', '검색어3'])

for 공고 in 검색결과:
    본문 = "[" + 공고["공고기관"] + "]\n" + 공고["공고명"] + "\n" + 공고["입찰마감일시"] + "\n" + 공고["URL"]
    print(본문)
```

일단 나는 딕셔너리 형태로 검색 결과를 저장했다. 필요에 따라 필요한 항목만 꺼내 쓰기 편하기 때문.

그리고 이렇게 수집한 결과를 이메일로 보내는 코드를 추가하고, `schedule`을 통해 매주 같은 시간에 실행되도록 스케줄링 해놨다. (수집된 결과를 정리하거나 이메일 본문 형식으로 재구성하거나 스케줄링 하는 코드는 별도의 설명이 필요하므로 여기서는 생략한다.

> 아무튼 반복적이고 단순한 작업은 컴퓨터에게 맡기고 우리는 그 시간에 다른 걸 하자.