```python
input_cleaner="""
4 3 2
25
1 1 1 3 1 1 1 2 2 1 2 1 1 1 2 1 3 1 1 1 1 1 1 2 1
"""
inputs = input_cleaner.split('\n')[1:-1][::-1]
def input():    return inputs.pop()

n, x, y = map(int, input().split())
x += (n*2-1)
y += (n-1)
k = int(input())
com = list(map(int, input().split()))

dx, dy, d_index = [-1,0,1,0], [0,-1,0,1], 0

mat = list([0]*n*(n-1) for _ in range((n-1)*n))
flag = 1

def sect(xx,yy,n):
    flag = 0
    if 0 <= xx < n and n <= yy < (n-2)*n:
        flag = 2
    elif n <= xx < (n-2)*n and n <= yy < (n-2)*n:
        flag = 5
    elif n <= xx < (n-2)*n and 0 <= yy < n:
        flag = 4
    elif n <= xx < (n-2)*n and (n-2)*n <= yy < (n-1)*n:
        flag = 3
    elif (n-2)*n <= xx < (n-1)*n and n <= yy < (n-2)*n:
        flag = 1
    elif 0 <= xx < n and 0 <= yy < n:
        flag = 6
    elif 0 <= xx < n and (n-2)*n <= yy < (n-1)*n:
        flag = 7
    elif (n-2)*n <= xx < (n-1)*n and 0 <= yy < n:
        flag = 8
    elif (n-2)*n <= xx < (n-1)*n and (n-2)*n <= yy < (n-1)*n:
        flag = 9
    else:
        pass
    return flag


def cross(pf, nf, di):
    cube = [1,9,3,7,2,6,4,8]
    if cube[cube.index(pf)+1] == nf:
        nx, ny = py, px
        di += 1
    elif cube[cube.index(pf)-1] == nf:
        nx, ny = py, px
        di -= 1
    else:
        pass
    return [di, nx, ny]


def fun(px, py, direc_index, com, flag, n):
    pf, nx, ny = flag, px, py
    if com == 1 and 0 <= nx < (n-1)*n and 0 <= ny < (n-1)*n:
        nx += dx[direc_index]
        ny += dy[direc_index]
    elif com == 2:
        direc_index += 1
    else:
        direc_index -= 1

    nf = sect(nx, ny, n)
    if pf != nf:
        [direc_index, nx, ny] = cross(pf, nf, direc_index)
    return [nx, ny, direc_index, flag]

def result(x, y, d_index, n):
    # dx, dy, d_index = [-1,0,1,0], [0,-1,0,1], 0
    direc = ['상','좌','하','우']
    print('x 위치: %d, y 위치: %d, 방향: %s, 분면: %d' %(x, y, direc[d_index], sect(x,y,n)))
    return

[x, y, d_index, flag] = fun(x, y, d_index, 1, flag, n)
result(x, y, d_index, n)
[x, y, d_index, flag] = fun(x, y, d_index, 1, flag, n)
result(x, y, d_index, n)
[x, y, d_index, flag] = fun(x, y, d_index, 1, flag, n)
result(x, y, d_index, n)
```
