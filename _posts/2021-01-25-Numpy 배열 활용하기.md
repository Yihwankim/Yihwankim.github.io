### numpy는 일종의 짱짱맨 함수로 유사한 형태의 데이터를 배열로서 표시할때 효과만점이다.


```python
import numpy as np
```


```python
# 1차원 array 만들기 
a1 = np.array([1,2,3])
print(a1)
```

    [1 2 3]
    


```python
# make the array as 2-dimension, using [[]] this type.
a2 = np.array([[1,2,3],[4,5,6]])
print(a2)
```

    [[1 2 3]
     [4 5 6]]
    


```python
# Make the array as a 3-dimension, using [[[]]] type.
a3 = np.array([[[1,2,3], [4,5,6]],[[1,2,3], [4,5,6]]])
print(a3)
```

    [[[1 2 3]
      [4 5 6]]
    
     [[1 2 3]
      [4 5 6]]]
    


```python
#np.arange(시작번호, 끝번호, 간격)
c = np.arange(2,8,2)
print(c)
```

    [2 4 6]
    


```python
print(np.arange(15)) #0부터 15직전까지 1씩 건너뛰며 숫자를 배열
print(np.arange(5,15))
```

    [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]
    [ 5  6  7  8  9 10 11 12 13 14]
    

#### 배열모양을 변경할 때는 배열명.reshape(행,열) 함수를 사용


```python
c = np.arange(15)
c = c.reshape(3, 5)
print(c)
#1*15 를 3*5로 바꿀 수 있다
```

    [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]]
    


```python
d = np.arange(0,100,5)
print(d)
```

    [ 0  5 10 15 20 25 30 35 40 45 50 55 60 65 70 75 80 85 90 95]
    


```python
d = d.reshape(2,2,5)
d
```




    array([[[ 0,  5, 10, 15, 20],
            [25, 30, 35, 40, 45]],
    
           [[50, 55, 60, 65, 70],
            [75, 80, 85, 90, 95]]])




```python
# nd operation
n1 = np.array([[1,0],
              [0,1]])
n2 = np.array([[1,2],
              [3,4]])
```


```python
n1 + n2
```




    array([[2, 2],
           [3, 5]])




```python
n1 - n2
```




    array([[ 0, -2],
           [-3, -3]])




```python
n1 * n2 #행렬 연산이 아닌, 스칼라 연산을 실시한다... 같은 위치의 원소끼리 곱함
```




    array([[1, 0],
           [0, 4]])




```python
n1 / n2
```




    array([[1.  , 0.  ],
           [0.  , 0.25]])




```python
n2/n1 #infinite 는 어떻게?
```

    C:\Users\a\anaconda3\lib\site-packages\ipykernel_launcher.py:1: RuntimeWarning: divide by zero encountered in true_divide
      """Entry point for launching an IPython kernel.
    




    array([[ 1., inf],
           [inf,  4.]])




```python
n1 + 1 #각 원소에 1을 더해줌
```




    array([[2, 1],
           [1, 2]])




```python
n2 * 2
```




    array([[2, 4],
           [6, 8]])




```python
n2/100 #data를 지수화 하는데 필요
```




    array([[0.01, 0.02],
           [0.03, 0.04]])



### 벡터연산, dot() 함수를 사용!


```python
np.dot(n1,n2)
```




    array([[1, 2],
           [3, 4]])




```python
n1.dot(n2) #곱셈을 다른 방식으로 표현
```




    array([[1, 2],
           [3, 4]])



### 줄끼리 연산을 해보자, 
##### axis=0 = column
##### axis=1 = row


```python
# 배열 내 연산
n3 = np.arange(12).reshape(3,4) #0~11까지 1간격으로 배열을 만들고 이를 3*4 형태로 변환
```


```python
print(n3)
```

    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    


```python
n3.sum(axis=0) #sum of cloumn
```




    array([12, 15, 18, 21])




```python
n3.sum(axis=1) #sum of row
```




    array([ 6, 22, 38])




```python
n3.max(axis=1) # the max value of each row
```




    array([ 3,  7, 11])




```python
n3.mean(axis=0) #the mean value of each column
```




    array([4., 5., 6., 7.])




```python
#axis를 지정하지 않으면 전체 행렬에서 각 value를 출력한다
print(n3.max())
print(n3.min())
print(n3.mean())
print(n3.sum())
print(n3.std())
print(n3.var())
print(n3.cumsum()) # 각 원소까지의 누적 합
print(n3.cumprod()) # 각 원소까지의 누적 곱
```

    11
    0
    5.5
    66
    3.452052529534663
    11.916666666666666
    [ 0  1  3  6 10 15 21 28 36 45 55 66]
    [0 0 0 0 0 0 0 0 0 0 0 0]
    

### 배열에서 특정 데이터를 뽑아오는 slicing 기능
#### 슬라이싱 주소 1은 2번째 데이터를 지칭함을 명심할 것


```python
v = np.array([[11,12,13,14,15],
              [21,22,23,24,25],
              [31,32,33,34,35],
              [41,42,43,44,45],
              [51,52,53,54,55]])
```


```python
# array에서 data 뽑아오기
v[1,2] #2행 3열 data 추출, ~~ 1행 2열이 아니다!!
```




    23




```python
v[1][2]
```




    23




```python
v[:,1] #전체 행, 2열 data 추출
```




    array([12, 22, 32, 42, 52])




```python
v[1:3,1] #1:3이 의미하는 것은 2,3,4행이 아닌 2,3행 a:b = a부터 b전 까지
```




    array([22, 32])




```python
v[2,1:3]
```




    array([32, 33])




```python
v[1:4,1:4] #2,3,4행 2,3,4열
```




    array([[22, 23, 24],
           [32, 33, 34],
           [42, 43, 44]])


