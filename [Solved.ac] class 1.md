```
input_1157="""
zZa
"""
inputs = input_1157.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

word = input().upper()
cnt, spel = [], []
for i in list(set(word)):
    spel.append(i)
    cnt.append(word.count(i))
if cnt.count(max(cnt)) >=2:
    print('?')
else:
    print(spel[cnt.index(max(cnt))])
```
```
input_2884="""
23 40
"""
inputs = input_2884.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

h, m = map(int, input().split())
if m < 45:
    rm = 60 - (45 - m)
    if h == 0:
        rh = 24 - 1
    else:
        rh = h - 1
elif m >= 45:
    rm = m - 45
    rh = h
print('%d %d' % (rh, rm))
```
```
# 백준 10171번
#\    /\
# )  ( ')
#(  /  )
# \(__)|

print('\\    /\\')
print(" )  ( ')")
print("(  /  )")
print(" \(__)|")
```
```
# 백준 10172번
#|\_/|
#|q p|   /}
#( 0 )"""\
#|"^"`    |
#||_/=\\__|
print("|\_/|")
print("|q p|   /}")
print('( 0 )"""\\')  # \'앞에 \을 붙여준다.
print('|"^"`    |')
print("||_/=\\\__|")  # \\ 앞에 \을 하나 더 붙여준다.
```
```
input_10951="""
1 1
2 3
3 4
9 8
5 2
"""
inputs = input_10951.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

while(1):
    try:
        a, b = map(int, input().split())
        print(a+b)
    except:
        break

```
