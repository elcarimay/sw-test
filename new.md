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

mat = list([0]*n*3 for _ in range(3*n))
flag = 1

def sect(xx,yy,n):
    flag = 0
    if 0 <= xx < n and n <= yy < 2*n:
        flag = 2
    elif n <= xx < 2*n and n <= yy < 2*n:
        flag = 5
    elif n <= xx < 2*n and 0 <= yy < n:
        flag = 4
    elif n <= xx < 2*n and 2*n <= yy < 3*n:
        flag = 3
    elif 2*n <= xx < 3*n and n <= yy < 2*n:
        flag = 1
    else:
        pass
    return flag


def cross(pf,nf,px,py,di):
    cube = [1,3,2,4]
    if cube[cube.index(pf)+1] == nf:
        nx, ny = py, px
        di += 1
    else:
        nx, ny = py, px
        di -= 1
    return [nx, ny, di]


def fun(px, py, direc_index, com, flag):
    pf = flag
    if com == 1:
        nx += dx[direc_index]
        ny += dy[direc_index]
        if nx < 0 or nx >= 3*n or ny < 0 or ny >= 3*n:
            pass
        elif:

        
    elif com == 2:
        direc_index += 1
    else:
        direc_index -= 1
    nf = sect(x0, y0, n)
    if pf != nf:
        [x0, y0, direc_index] = cross(pf, nf, x0, y0, direc_index)
    

    return [x0, y0, direc_index, flag]



# [x, y, d_index, flag] = fun(x, y, d_index, flag)
# print(x, y, d_index)
# [x, y, d_index, flag] = fun(x, y, d_index, flag)
# print(x, y, d_index)

# print(x, y, d_index, flag)
# [x, y, d_index, flag] = fun(x, y, d_index, 1, flag)
# print(x, y, d_index, flag)
# [x, y, d_index, flag] = fun(x, y, d_index, 1, flag)
# print(x, y, d_index, flag)
# [x, y, d_index, flag] = fun(x, y, d_index, 1, flag)
# print(x, y, d_index, flag)
sect(8,8,n)
