# 브루트 포스(Brute Force)  

## 1748번 수 이어쓰기 1(Silver IV)

규칙성을 찾아서 푸는 문제임  
한 자릿수는 19, 두자릿수는 1099, 세자릿수는 100~999  
한자릿수를 모두 더하면 9(9가지 X 자릿수(1))  
두자릿수를 모두 더하면 180(90가지 X 자릿수(2))  
세자릿수를 모두 더하면 2700(900가지 X 자릿수(3))  
```python
input_1748="""
3
5
15
120
"""
inputs = input_1748.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## main
n = int(input())
for _ in range(n):
    answer = 0

    num = input()
    for i in range(len(num)-1):
        answer += 9 * 10 ** i * (i+1)
    answer += (int(num) - 10 ** (len(num) - 1) + 1) * len(num) # 15인 경우 10 11 12 13 14 15 를 계산

    print(answer)
```
# 9095번 1, 2, 3 더하기(Silver III)

규칙성을 찾아낸 뒤 DP를 이용하여 풀이하는 문제이고 재귀로도 풀이 가능함  
우선 숫자가 작은 경우 직접 개수를 세어 규칙성을 찾는다.  

1일 때 -> 1  
2일 때 -> 2  
3일 때 -> 4  
4일 때 -> 7  
5일 때 -> 13  

이에 따라 점화식은 f(n) = f(n-1) + f(n-2) + f(n-3) (n>3 인 경우)  
```python
input_9095 = """
3
4
7
10
"""
## DP - bottom-up

inputs = input_9095.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

cache = [0] * 11
cache[1], cache[2], cache[3] = 1, 2, 4

for i in range(4, 11):
    cache[i] = sum(cache[i-3:i])

t = int(input())
for _ in range(t):
    print(cache[int(input())])
```
```python
inputs = input_9095.split('\n')[1:-1][::-1]
def input(): return inputs.pop()
def sol(num):
    if num == 1:
        return 1
    elif num == 2:
        return 2
    elif num == 3:
        return 4
    else: return sol(num-1) + sol(num-2) + sol(num-3)

## Recursion
t = int(input())
for _ in range(t):
    num = input()
    print(sol(int(num)))
```
# 브루트 포스 - N과 M  
## Python docs내용  

|함수|변수|설명|
|------|---|---|
|product()|p, q, … [repeat=1]|데카르트 곱(cartesian product), 중첩된 for 루프와 동등|
|permutations()|p[, r]|r-길이 튜플들, 모든 가능한 순서, 반복되는 요소 없음|
|combinations()|p, r|r-길이 튜플들, 정렬된 순서, 반복되는 요소 없음|
|combinations_with_replacement()|p, r|r-길이 튜플들, 정렬된 순서, 반복되는 요소 있음|

|Code|예시|정렬|반복|
|------|---|---|---|
|product('ABCD', repeat=2)|AA AB AC AD BA BB BC BD CA CB CC CD DA DB DC DD|X|O|
|permutations('ABCD', 2)|AB AC AD BA BC BD CA CB CD DA DB DC|X|X|
|combinations('ABCD', 2)|AB AC AD BC BD CD|O|X|
|combinations_with_replacement('ABCD', 2)|AA AB AC AD BB BC BD CC CD DD|O|O|

## 15649번 N과 M(1) (Silver III)  

permutation	정렬 X, 반복 X
```python
input_15649="""
3
3 1
4 2
4 4
"""

inputs = input_15649.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(a)):
        if a[i] not in selected:
            dfs(selected + [a[i]])

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))

    dfs()
```
```python
inputs = input_15649.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(a)):
        if a[i] not in selected:
            selected.append(a[i])
            dfs(selected)
            selected.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()

    
    a = list(range(1,int(n)+1))

    dfs()
```
```python
from itertools import permutations

inputs = input_15649.split('\n')[1:-1][::-1]
def input(): return inputs.pop()
tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    
    for i in permutations(range(1,int(n)+1),int(m)):
        print(*i)
```
## 15650번 N과 M(2) (Silver III)  

combination 정렬 O, 반복 X
```python
input_15650="""
3
3 1
4 2
4 4
"""

inputs = input_15650.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
def dfs(start,selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(start, len(a)):
        if a[i] not in selected:
            dfs(i+1,selected + [a[i]])

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))

    dfs(0)
```
```python
inputs = input_15650.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
def dfs(start,selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(start, len(a)):
        if a[i] not in selected:
            selected.append(a[i])
            dfs(i+1,selected)
            selected.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))

    dfs(0)
```
```python
from itertools import combinations
inputs = input_15650.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()

    if 1:
        for i in combinations([str(x) for x in range(1, int(n)+1)], int(m)):
            print(*i)
    if 0:
        print("\n".join(map(" ".join, combinations([str(x) for x in range(1, int(n)+1)], int(m)))))
```
## 15651번 N과 M(3) (Silver III)  

product	정렬 X, 반복 O
```python
input_15651="""
3
3 1
4 2
3 3
"""

inputs = input_15651.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(a)):
        dfs(selected + [a[i]])

tc = input()
for _ in range(int(tc)):
    n, m = input().split()


    a = list(range(1,int(n)+1))

    dfs()
```
```python
inputs = input_15651.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(a)):
        selected.append(a[i])
        dfs(selected)
        selected.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))
    dfs()
```
```python
from itertools import product
inputs = input_15651.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    for i in product(range(1,int(n)+1), repeat=int(m)):
        print(*i)
```
## 15652번 N과 M(4) (Silver III)  

combination with replacement 정렬 O, 반복 O
```python
input_15652="""
3
3 1
4 2
3 3
"""

inputs = input_15652.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
def dfs(start,selected=[]):
    if len(selected) == int(m):            
        print(*selected)
        return
    for i in range(start, len(a)):
        dfs(i,selected + [a[i]])

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))

    dfs(0)
```
```python
inputs = input_15652.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
def dfs(start,selected=[]):
    if len(selected) == int(m):            
        print(*selected)
        return
    for i in range(start, len(a)):
        selected.append(a[i])
        dfs(i,selected)
        selected.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    a = list(range(1,int(n)+1))

    dfs(0)
```
```python
from itertools import combinations_with_replacement

inputs = input_15652.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

tc = input()
for _ in range(int(tc)):
    n, m = input().split()
    for i in combinations_with_replacement(range(1, int(n)+1), int(m)):
        print(*i)
```
## 15654번 N과 M(5) (Silver III)  

permutation	정렬 X, 반복 X
```python
input_15654="""
4 2
9 8 7 1
"""

inputs = input_15654.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(arr)):
        if arr[i] not in selected:
            dfs(selected + [arr[i]])

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()


dfs()
```
```python
inputs = input_15654.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(arr)):
        if arr[i] not in selected:
            selected.append(arr[i])
            dfs(selected)
            selected.pop()  ## Backtracking

n, m = inputs.pop().split()
arr = list(map(int, inputs.pop().split()))
arr.sort()

dfs()
```
```python
from itertools import permutations

inputs = input_15654.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
for i in permutations(arr,int(m)):
    print(*i)
```
## 15655번 N과 M(6) (Silver III)  

combination 정렬 O, 반복 X
```python
input_15655="""
4 4
1231 1232 1233 1234
"""

inputs = input_15655.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
n, m = inputs.pop().split()
arr = list(map(int, input().split()))
arr.sort()
def dfs(start, selected=[]):
    if len(selected) == int(m):
        result.append(selected[:]) ## deep copy
        return
    for i in range(start,len(arr)):
        # if arr[i] not in selected:
        selected.append(arr[i])
        dfs(i+1,selected)
        selected.pop()  ## Backtracking
result = []
dfs(0)

for i in result:
    print(*i)
```
```python
from itertools import combinations
inputs = input_15655.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
for i in combinations(arr, int(m)):
    print(*i)
```
## 15656번 N과 M(7) (Silver III)  

product	정렬 X, 반복 O
```python
input_15656="""
4 2
9 8 7 1
"""

inputs = input_15656.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()

## 순열 ver 1.0
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(arr)):
        dfs(selected + [arr[i]])
dfs()
```
```python
input_15656="""
4 2
9 8 7 1
"""

inputs = input_15656.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()

## 순열 ver 1.1 및 Backtracing
def dfs(selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(len(arr)):
        selected.append(arr[i])
        dfs(selected)
        selected.pop()
dfs()
```
```python
from itertools import product
inputs = input_15656.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
for i in product(arr, repeat=int(m)):
    print(*i)
```
## 15657번 N과 M(8) (Silver III)  

combination with replacement 정렬 O, 반복 O
```python
input_15657="""
4 2
9 8 7 1
"""

inputs = input_15657.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.0
n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
def dfs(start, selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(start, len(arr)):
        dfs(i, selected + [arr[i]])
dfs(0)
```
```python
input_15657="""
4 2
9 8 7 1
"""

inputs = input_15657.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## 순열 ver 1.1 및 Backtracing
n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
def dfs(start, selected=[]):
    if len(selected) == int(m):
        print(*selected)
        return
    for i in range(start, len(arr)):
        selected.append(arr[i])
        dfs(i, selected)
        selected.pop()
dfs(0)
```
```python
from itertools import combinations_with_replacement

inputs = input_15657.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

n, m = input().split()
arr = list(map(int, input().split()))
arr.sort()
for i in combinations_with_replacement(arr, int(m)):
    print(*i)
```

