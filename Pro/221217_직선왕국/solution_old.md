```cpp
#if 1 // 103 ms
#include <cmath>
#include <deque>

#define manager_arrive_capital 0
#define carriage_arrive_city 1
#define manager_arrive_city 2
#define manager_leave_capital 3
#define MAXCITY 200
#define MAXORDER 10000

using namespace std;

class Data {
public:
    int tStamp;
    int event;
    int grain;
    int city;

    Data() {}
    Data(int time, int ev, int g, int c) {
        tStamp = time;
        event = ev;
        grain = g;
        city = c;
    }
};

class Heap {
public:
    Data arr[MAXORDER];
    int length;

    void init() { length = 0; }

    bool compare(int parent, int child) {
        if (arr[parent].tStamp > arr[child].tStamp) { return true; }
        if (arr[parent].tStamp == arr[child].tStamp && arr[parent].event > arr[child].event) { return true; }
        return false;
    }

    void push(int time, int event, int grain, int city) {
        Data last = Data(time, event, grain, city);
        arr[length] = last;

        int idx = length;
        while ((idx - 1) / 2 >= 0 && compare((idx - 1) / 2, idx)) {
            Data temp = arr[idx];
            arr[idx] = arr[(idx - 1) / 2];
            arr[(idx - 1) / 2] = temp;
            idx = (idx - 1) / 2;
        }
        length++;
    }

    Data pop() {
        Data ans = arr[0];
        length--;
        arr[0] = arr[length];

        int idx = 0;
        int left, right, child;
        while (2 * idx + 1 < length) {
            left = 2 * idx + 1;
            right = 2 * idx + 2;

            if (right < length) {
                if (compare(left, right)) { child = right; }
                else { child = left; }
            }
            else { child = left; }

            if (compare(idx, child)) {
                Data temp = arr[idx];
                arr[idx] = arr[child];
                arr[child] = temp;
                idx = child;
            }
            else { break; }
        }
        return ans;
    }
};


class City {
public:
    bool isDispatched;
    int grain;
    deque< pair<int, int> > expected;
};

int managers, totalCity;
City cities[MAXCITY];
Heap nextEvent;

void dispatch(int tStamp) {
    while (managers > 0) {
        int city_dispatched = 0;
        int grain = 0;

        for (int i = 1; i < totalCity; i++) {
            if (cities[i].isDispatched) { continue; }

            int grainInCity = cities[i].grain;
            for (int k = 0; k < cities[i].expected.size(); k++) {
                if (cities[i].expected[k].first <= tStamp + i) {
                    grainInCity += cities[i].expected[k].second;
                }
            }

            if (grainInCity > grain) {
                grain = grainInCity;
                city_dispatched = i;
            }
        }
        if (city_dispatched > 0) {
            managers--;
            cities[city_dispatched].isDispatched = true;
            nextEvent.push(tStamp + city_dispatched, manager_arrive_city, 0, city_dispatched);
        }
        else { break; }
    }
}

void updateTime(int tStamp) {
    while (nextEvent.length > 0 && nextEvent.arr[0].tStamp <= tStamp) {
        Data curr = nextEvent.pop();
        if (curr.event == manager_arrive_capital) {
            cities[0].grain += curr.grain;
            managers++;
            cities[curr.city].isDispatched = false;
        }
        else if (curr.event == carriage_arrive_city) {
            cities[curr.city].grain += curr.grain;
            while (cities[curr.city].expected.front().first <= curr.tStamp && !cities[curr.city].expected.empty()) {
                cities[curr.city].expected.pop_front();
            }
        }
        else if (curr.event == manager_arrive_city) {
            nextEvent.push(curr.tStamp + curr.city, manager_arrive_capital, cities[curr.city].grain, curr.city);
            cities[curr.city].grain = 0;
        }
        dispatch(curr.tStamp);
    }
}

void init(int N, int M) {
    managers = M;
    totalCity = N;

    for (int i = 0; i < N; i++) {
        cities[i].grain = 0;
        cities[i].isDispatched = false;
        cities[i].expected.clear();
    }
    nextEvent.init();
}

void destroy() {

}

int order(int tStamp, int mCityA, int mCityB, int mTax) {
    updateTime(tStamp - 1);

    int distance = abs(mCityB - mCityA);
    int carriage_arrive_time = tStamp + distance;
    nextEvent.push(carriage_arrive_time, carriage_arrive_city, mTax, mCityB);

    cities[mCityB].expected.push_back(make_pair(carriage_arrive_time, mTax));

    int manager_leave_time = max(carriage_arrive_time - mCityB, tStamp);
    nextEvent.push(manager_leave_time, manager_leave_capital, 0, mCityB);

    updateTime(tStamp);

    return cities[0].grain;
}

int check(int tStamp) {
    updateTime(tStamp);

    return cities[0].grain;
}
#endif // 0

```
