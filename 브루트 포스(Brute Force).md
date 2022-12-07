# 브루트 포스(Brute Force)  

## 1748번 수 이어쓰기 1(Silver IV)

규칙성을 찾아서 푸는 문제임  
한 자릿수는 19, 두자릿수는 1099, 세자릿수는 100~999  
한자릿수를 모두 더하면 9(9가지 X 자릿수(1))  
두자릿수를 모두 더하면 180(90가지 X 자릿수(2))  
세자릿수를 모두 더하면 2700(900가지 X 자릿수(3))  
```
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
```
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
```
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
