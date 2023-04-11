# [Solution.py]
```python
import heapq
from collections import defaultdict, deque

manager_arrive_capital = 0
carriage_arrive_city = 1
manager_arrive_city = 2
manager_leave_capital = 3

class City:
    def __init__(self):
        self.isDispatched = False
        self.grain = 0
        self.expected = deque()

cities = defaultdict(lambda: City())
nextEvent = []

def dispatch(tStamp):
    global managers; global city_dispatched; global grain; global totalCity
    while(managers > 0):
        city_dispatched, grain = 0, 0

        for i in range(1, totalCity):
            if cities[i].isDispatched:
                continue
            grainInCity = cities[i].grain
            for k in range(len(cities[i].expected)):
                if cities[i].expected[k][0] <= tStamp + i:
                    grainInCity += cities[i].expected[k][1]
            if grainInCity > grain:
                grain = grainInCity
                city_dispatched = i
        if city_dispatched > 0:
            managers -= 1
            cities[city_dispatched].isDispatched = True
            heapq.heappush(nextEvent, (tStamp + city_dispatched, manager_arrive_city, 0, city_dispatched))
        else:
            break


def updateTime(tStamp):
    global managers
    while len(nextEvent) > 0 and nextEvent[0][0] <= tStamp:
        tStamp2, event, grain, city = heapq.heappop(nextEvent)
        if event == manager_arrive_capital:
            cities[0].grain += grain
            managers += 1
            cities[city].isDispatched = False
        elif event == carriage_arrive_city:
            cities[city].grain += grain
            while len(cities[city].expected) and cities[city].expected[0][0] <= tStamp2:
                    cities[city].expected.popleft()
        elif event == manager_arrive_city:
            heapq.heappush(nextEvent, (tStamp2+city, manager_arrive_capital, cities[city].grain, city))
            cities[city].grain = 0
        dispatch(tStamp2)


def init(n, m):
    global managers
    global totalCity
    managers, totalCity = m, n
    for i in range(n):
        cities[i].grain = 0
        cities[i].isDispatched = False
        cities[i].expected = deque()
    global nextEvent
    nextEvent = []

# def destroy():
#     pass


def order(tStamp, mCityA, mCityB, mTax):
    updateTime(tStamp - 1)
    carriage_arrive_time = tStamp + abs(mCityB - mCityA)
    heapq.heappush(nextEvent, (carriage_arrive_time, carriage_arrive_city, mTax, mCityB))
    cities[mCityB].expected.append((carriage_arrive_time, mTax))
    manager_leave_time = max(carriage_arrive_time - mCityB, tStamp)
    heapq.heappush(nextEvent, (manager_leave_time, manager_leave_capital, 0, mCityB))
    updateTime(tStamp)

    return cities[0].grain


def check(tStamp):
    updateTime(tStamp)
    return cities[0].grain
```
# [Main.py]
```python
import sys
import solution
from time import time


CMD_INIT = 100
CMD_DESTROY = 200
CMD_ORDER = 300
CMD_CHECK = 400

order2 = {100: "init",
         200: "destroy",
         300: "order",
         400: "check",}

def run(k):
    ok = True
    query = [int(i) for i in k.readline().strip('\n').rstrip(' ').split(' ')]

    for _ in range(query[0]):
        cmd, *param = [int(i) for i in k.readline().strip('\n').rstrip(' ').split(' ')]
        print(f">> {order2[cmd]}", *param)

        if cmd == CMD_INIT:
            N, M = param
            solution.init(N, M)

        if cmd == CMD_DESTROY:
            pass

        if cmd == CMD_ORDER:
            tStamp, mCityA, mCityB, mTax, answer = param
            result = solution.order(tStamp, mCityA, mCityB, mTax)
            if answer != result:
                ok = False

        if cmd == CMD_CHECK:
            tStamp, answer = param
            result = solution.check(tStamp)
            if answer != result:
                ok = False

        if not ok:
            break

    return ok


if __name__ == '__main__':

    start = time()
    fo = open('C:\\Program1\\WPy64-31101\\notebooks\\01_Pro_test\\sample_input_직선왕국.txt', 'r')
    sys.stdin = fo
    k = sys.stdin
    TC, MARK = [int(i) for i in k.readline().strip('\n').rstrip(' ').split(' ')]

    TC = 5
    for case in range(1, TC + 1):
        score = MARK if run(k) else 0
        print("#%d %d" % (case, score), flush=True)
    
    fo.close()
    print(f">> Time = {time() - start:.5f} [sec]")
```
