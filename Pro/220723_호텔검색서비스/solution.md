```cpp
#if 1
#include <set>
#include <vector>
#include <algorithm>
#include <queue>
using namespace std;

struct Date {
	int in, out;
	bool operator<(const Date r)const {
		return (in > r.in) ||
			(in == r.in && out > r.out);
	}
};

vector<int> roomList[1003];
struct RoomInfo {
	int hash, price;
	vector<Date> date;
}room[100003];

struct Data {
	int rid, price;
	bool operator<(const Data& r)const {
		return (price > r.price) ||
			(price == r.price && rid > r.rid);
	}
};
priority_queue<Data> pq[10003];

void init(int N, int mRoomCnt[]) {
	for (int i = 1; i <= N; i++) roomList[i].clear();
	for (auto& a : pq) a = {};
}

int gethash(int* a) {
	return (a[0] - 1) * 1000 + (a[1] - 1) * 100 + a[2] * 10 + a[3];
}

void addRoom(int hid, int rid, int info[]) {
	roomList[hid].push_back(rid);
	int hash = gethash(info), price = info[4];
	room[rid] = { hash, price };
	pq[hash].push({ rid, price });
}

bool overlap(int rid, int s, int e) {
	for (int i = 0; i < room[rid].date.size(); i++)
		if (!(room[rid].date[i].out <= s || e <= room[rid].date[i].in)) return 1;
	return 0;
}

int findRoom(int f[]) {
	int s = f[0], e = f[1], hash = gethash(f + 2);
	vector<Data> backup;
	int ret = -1;
	while (pq[hash].size()) {
		auto cur = pq[hash].top(); pq[hash].pop();
		if (cur.price != room[cur.rid].price) continue;
		backup.push_back({ cur.rid, cur.price });
		if (overlap(cur.rid, s, e)) continue;
		room[ret = cur.rid].date.push_back({ s, e });
		break;
	}
	for (int i = 0; i < backup.size(); i++) pq[hash].push(backup[i]);
	return ret;
}

int riseCosts(int hid) {
	int ret = 0;
	for (int i = 0; i < roomList[hid].size(); i++) {
		int hash = room[roomList[hid][i]].hash;
		int& price = room[roomList[hid][i]].price;

		price += price / 10;
		pq[hash].push({ roomList[hid][i], price });
		ret += price;
	}
	return ret;
}
#endif
```
