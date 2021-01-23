---
layout: single
title: "BeautifulSoup을 이용해 **크롤링** 해보기 (왕기초)"
---


```python
html_doc = """
<html><head><title>The Office</title></head>
<body>
<p class="title"><b>The Office</b></p>
<p class="story">In my office, there are four officers,
<a href="http://example.com/YW" class="member">YW</a>,
<a href="http://example.com/JK" class="member">JK</a>,
<a href="http://example.com/YJ" class="member">YJ</a> and
<a href="http://example.com/KS" class="member">KS</a>
.</p>
<p class="story">...</p>
"""
```


```python
from bs4 import BeautifulSoup
```

# BeautifulSoup '함수'의 경우  
## 함수들이 모여서 모듈이 되고, 모듈이 모여서 라이브러리가 된다.

# *위에 글은 markdown 형식으로 처리한것 *

"""이것은 주석"""


```python
'''주석 안되나?'''
```




    '주석 안되나?'




```python
soup = BeautifulSoup(html_doc, 'html.parser')
#BeautifulSoup 함수를 이용해 html_doc을 해석, soup에 저장
```


```python
#위에서는 html 코드가 다소 더러웠으므로, 
#prettify함수를 이용해 태그 단위별 줄바꿈 실시
print(soup.prettify())
```

    <html>
     <head>
      <title>
       The Office
      </title>
     </head>
     <body>
      <p class="title">
       <b>
        The Office
       </b>
      </p>
      <p class="story">
       In my office, there are four officers,
       <a class="member" href="http://example.com/YW">
        YW
       </a>
       ,
       <a class="member" href="http://example.com/JK">
        JK
       </a>
       ,
       <a class="member" href="http://example.com/YJ">
        YJ
       </a>
       and
       <a class="member" href="http://example.com/KS">
        KS
       </a>
       .
      </p>
      <p class="story">
       ...
      </p>
     </body>
    </html>
    


```python
#제목_title 만 가져와보자
soup.title #title tag를 통째로 가져옴
```




    <title>The Office</title>




```python
soup.title.text #<태그> 는 필요하지 않으므로 내용만 출력
```




    'The Office'




```python
 # 개별 이름 YW, JK, YJ, KS의 경우 <a> 태그로 쌓여있다. 
## <a> 태그를 중심으로 확인하면, 
soup.a
```




    <a class="member" href="http://example.com/YW">YW</a>




```python
# 1명의 이름만 나왔다. 전체 이름을 보기 위해서는 find_all을 써야함
```


```python
soup.find_all('a') # a 태그를 모두 가져옴
```




    [<a class="member" href="http://example.com/YW">YW</a>,
     <a class="member" href="http://example.com/JK">JK</a>,
     <a class="member" href="http://example.com/YJ">YJ</a>,
     <a class="member" href="http://example.com/KS">KS</a>]




```python
soup.find_all('a')[2] 
#python은 숫자를 0부터 카운트하므로 2는 3번째 사람의 이름 
#리스트명[숫자]
```




    <a class="member" href="http://example.com/YJ">YJ</a>




```python
member = soup.find_all('a')
for k in member:
    print(k.text)
    
    # for 을 이용해서 soup.find_all('a') 안의 모든 내용을 불러오기. 
    # k 대신 어떤 문자를 써도 됨
```

    YW
    JK
    YJ
    KS
    


## html의 기본 양식
'''
<html>
<head>
   <title> 제목 </title>
   <script> the script program </script>
</head>
<body>
   <a href="링크 주소"> 다른 웹페이지로 연결할 수 있는 하이퍼링크 </a>
   <p> 문단 </p>
   <div> 블록 </div>
   <b> 볼드체 </b>
   <table> table 
    <tr> the row of table 
       <td> the column of table : the data is recorded </td>
    </tr>
   </table>
</body>
</html>
'''


```python

```
