```cpp
#if 1
#include <unordered_map>
#include <set>
using namespace std;

#define MAXC 70003
#define NONE 0
#define PARK 1
#define WAIT 2

struct Data {
	int calTime, startTime, cid;
	bool operator < (const Data& r)const {
		if (calTime != r.calTime) return calTime > r.calTime;
		return startTime < r.startTime;
	}
};

struct Car
{
	int state; // 0:none, 1:park, 2:wait
	int startTime, parkTime, waitTime;
	set<Data>::iterator it;
}car[MAXC];

unordered_map<int, int> idmap; // carid, cnt
int carCnt;

set<Data> wait;
int baseTime, baseFee, unitTime, unitFee, capacity;
void init(int mBaseTime, int mBaseFee, int mUnitTime, int mUnitFee, int mCapacity) {
	baseTime = mBaseTime; baseFee = mBaseFee; unitTime = mUnitTime;
	unitFee = mUnitFee; capacity = mCapacity;
	idmap.clear(); wait.clear(); carCnt = 0;
}

int getId(int mCar) {
	int cid;
	auto it = idmap.find(mCar);
	if (it == idmap.end()) {
		cid = carCnt;
		idmap[mCar] = carCnt++;
		car[cid] = { 0,0,0,0 };
	}
	else cid = it->second;
	return cid;
}

int arrive(int mTime, int mCar) {
	int cid = getId(mCar);
	auto& c = car[cid];
	c.startTime = mTime;
	if (capacity > 0) { // 주차공간이 있는 경우
		c.state = PARK;
		capacity--;
	}
	else { // 주차공간이 없을 때
		c.state = WAIT;
		c.it = wait.insert({ c.waitTime - c.parkTime - mTime, mTime, cid }).first;
	}
	return wait.size();
}

int leave(int mTime, int mCar) {
	int cid = getId(mCar), cal = 0;
	auto& c = car[cid];
	if (c.state == WAIT) { // 대기중인 경우
		wait.erase(c.it);
		c.state = NONE;
		c.waitTime += mTime - c.startTime;
		return -1;
	}
	else { // 주차중인 경우
		c.state = NONE;
		if (mTime - c.startTime <= baseTime) cal = baseFee;
		else cal = baseFee + ceil(double(mTime - c.startTime - baseTime) / unitTime) * unitFee;
		c.parkTime += mTime - c.startTime;
	}
	if (wait.size()) {
		cid = wait.begin()->cid;
		auto& c1 = car[cid];
		wait.erase(c1.it);
		c1.state = PARK;
		c1.waitTime += mTime - c1.startTime;
		c1.startTime = mTime;
	}
	else capacity++;
	return cal;
}
#endif // 1

```
