```
input_1018="""
8 8
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBBBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
"""
inputs = input_1018.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n, m = map(int, input().split())
board = list(input() for _ in range(n))
result = []
for i in range(n-7):
    for j in range(m-7):
        draw1, draw2 = 0, 0
        for a in range(i, i+8):
            for b in range(j, j+8):
                if (a+b)%2 == 0:
                    if board[a][b] != 'B':
                        draw1 += 1
                    if board[a][b] != 'W':
                        draw2 += 1
                else:
                    if board[a][b] != 'W':
                        draw1 += 1
                    if board[a][b] != 'B':
                        draw2 += 1
        result.append(draw1)
        result.append(draw2)
print(min(result))
```
```
input_1181="""
13
but
i
wont
hesitate
no
more
no
more
it
cannot
wait
im
yours
"""
inputs = input_1181.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n = int(input())
arr = list(input() for i in range(n))
arr = set(arr)
arr = list(arr)
arr.sort()
arr.sort(key = len)
for i in arr:
    print(i)
```
```
input_1181="""
500
"""
inputs = input_1181.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n =int(input())
nth = 666
count = 0

while True:
    if '666' in str(nth):
        count+=1
    if count == n:
        print(nth)
        break
    nth+=1
```
```
input_1654="""
4 11
802
743
457
539
"""
inputs = input_1654.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

k, n = map(int, input().split())
data = list(int(input()) for i in range(k))
start, end = 1, max(data)

## 이분탐색
while start <= end:
    mid = (start + end)//2
    lines = 0
    for i in data:
        lines += i // mid
    if lines >= n:
        start = mid + 1
    else:
        end = mid - 1
print(end)
```
```
input_1874="""
8
4
3
6
8
7
5
2
1
"""
inputs = input_1874.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n = int(input())
s, op, count, temp = [], [], 1, True

for i in range(n):
    num = int(input())
    while count <= num:
        s.append(count)
        op.append('+')
        count += 1
    if s[-1] == num:
        s.pop()
        op.append('-')
    else:
        temp = False
if temp == False:
    print('NO')
else:
    for i in op:
        print(i)
```
```
input_1920="""
5
4 1 5 2 3
5
1 3 7 9 5
"""
inputs = input_1920.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n = int(input())
n_arr = set(input().split()) # set 자료형내 찾기는 O(1)
m = int(input())
m_arr = input().split()

for i in m_arr:
    if i in n_arr:
        print(1)
    else:
        print(0)
```
```
input_1966="""
3
1 0
5
4 2
1 2 3 4
6 0
1 1 9 1 1 1
"""
inputs = input_1966.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

tc = int(input())

for _ in range(tc):
    n, m = map(int, input().split())
    m = int(m)
    imp = input().split()
    idx = list(range(len(imp)))
    idx[m] = 'target'

    order = 0
    while True:
        # 첫번째 if: imp의 첫번째 값 = 최대값?
        if imp[0] == max(imp):
            order += 1

            # 두번째 if: idx의 첫번째 값 = "target"?
            if idx[0] == 'target':
                print(order)
                break
            else:
                imp.pop(0)
                idx.pop(0)
        else:
            imp.append(imp.pop(0))
            idx.append(idx.pop(0))
```
```
input_2108="""
3
0
0
-1
"""
inputs = input_2108.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

from collections import Counter

n = int(input())
num = list(int(input()) for i in range(n))

# 산술평균 - 다 더해서 / n
print(int(round((sum(num)/n),0)))

# 중앙값 - 오름차순 -> 중앙값
num.sort()
print(num[n//2])

# 최빈값
cnt = Counter(num).most_common()
if len(cnt) > 1 and cnt[0][1]==cnt[1][1]: # 최빈값 2개 이상
    print(cnt[1][0])
else:
    print(cnt[0][0])

# 범위 - 최대값 - 최소값
print(max(num) - min(num))
# int(round((sum(num)/5),0))
```
```
input_2775="""
2
1
3
2
3
"""
inputs = input_2775.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

tc = int(input())
for _ in range(tc):
    k, n = int(input()), int(input())
    f0 = list(range(1, n + 1))
    for _ in range(k):
        for i in range(1, n):
            f0[i] += f0[i - 1]
    print(f0[-1])
```
```
input_2805="""
5 20
4 42 40 26 46
"""
inputs = input_2805.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n, m = map(int, input().split())
tree = list(map(int, input().split()))
start, end = 1, max(tree)

while start <= end:
    mid = (start + end) // 2
    log = 0
    for i in tree:
        if i >= mid:
            log += i - mid
    if log >= m:
        start = mid + 1
    else:
        end = mid - 1
print(end)
```
```
input_2839="""
11
"""
inputs = input_2839.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

sugar = int(input())
bag = 0

while sugar >= 0:
    if sugar % 5 == 0:
        bag += (sugar // 5)
        print(bag)
        break
    sugar -= 3
    bag += 1
else:
    print(-1)
```
```
input_2869="""
100 99 1000000000
"""
inputs = input_2869.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

import math

a, b, v = map(int, input().split())
day = (v - b) / (a - b)
print(math.ceil(day))
```
```
input_10814="""
3
21 Junkyu
21 Dohyun
20 Sunyoung
"""
inputs = input_10814.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n = int(input())
data = []
for _ in range(n):
    age, name = map(str, input().split())
    age = int(age)
    data.append((age, name))
data.sort(key = lambda x:x[0])

for i in data:
    print(*i)
```
```
input_10989="""
10
5
2
3
1
4
2
3
5
1
7
"""
inputs = input_10989.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n, data = int(input()), [0]*10001
for _ in range(n):
    data[int(input())] += 1

for i in range(10001):
    if data[i] != 0:
        for j in range(data[i]):
            print(i)
```
```
input_11650="""
5
3 4
1 1
1 -1
2 2
3 3
"""
inputs = input_11650.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n = int(input())
data = []
for i in range(n):
    data.append(list(map(int, input().rstrip().split())))
data.sort(key = lambda x:(x[0],x[1]))
for i in data:
    print(*i)
```
```
input_18111="""
3 4 0
64 64 64 64
64 64 64 64
64 64 64 63
"""
inputs = input_18111.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

## pypy3로 해결(다른건 시간초과됨)
import sys

n, m, b = map(int, input().split())
ground = list(list(map(int, input().split())) for _ in range(n))
ans, idx = sys.maxsize, 0

for target in range(257):
    max, min = 0, 0
    for i in range(n):
        for j in range(m):
            if ground[i][j] >= target:
                max += ground[i][j] - target
            else:
                min += target - ground[i][j]

    if max + b >= min:
        if min + (max * 2) <= ans:
            ans = min + (max * 2)
            idx = target

print(ans, idx)
```
```
input_15829="""
3
zzz
"""
inputs = input_15829.split('\n')[1:-1][::-1]
def input(): return inputs.pop()

def fun(string, r=31, M = 1234567891):
    A = list(ord(s) - ord('a')+1 for s in string)
    H = list(A[i]*r**i for i in range(len(A)))
    return sum(H) % M

n = int(input())
data = input()
print(fun(data))
```
