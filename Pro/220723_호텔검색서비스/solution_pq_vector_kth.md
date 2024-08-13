```cpp
#if 1 // 200 ms
/*
* 방 형태별 분류 : pq
* 방 예약시간 관리 : vector, linear search
*/
#include<vector>
#include<queue>
using namespace std;
using pii = pair<int, int>;

vector<int> roomList[1003];
struct RoomInfo
{
	int hash, price;
	vector<pii> date;
}room[100003];

priority_queue<pii, vector<pii>, greater<>> pq[10003];


void init(int N, int mRoomCnt[])
{
	for (int i = 1; i <= N; i++) roomList[i].clear();
	for (auto& a : pq) a = {};
}

int gethash(int* a) { return (a[0] - 1) * 1000 + (a[1] - 1) * 100 + a[2] * 10 + a[3]; }

void addRoom(int hid, int rid, int info[])
{
	roomList[hid].push_back(rid);
	int hash = gethash(info), price = info[4];
	room[rid] = { hash, price };
	pq[hash].push({ price, rid });
}

bool overlap(int rid, int s, int e)
{
	for (pii d : room[rid].date)
		if ((d.second <= s || e <= d.first) == 0) return 1;
	return 0;
}

int findRoom(int filter[])
{
	int s = filter[0], e = filter[1], hash = gethash(filter + 2);
	vector<pii> backup;
	int ret = -1;
	while (pq[hash].size())
	{
		int price = pq[hash].top().first;
		int rid = pq[hash].top().second;
		pq[hash].pop();
		if (price != room[rid].price) continue;
		backup.push_back({ price, rid });
		if (overlap(rid, s, e)) continue;
		room[rid].date.push_back({ s,e });
		ret = rid;
		break;
	}
	for (pii p : backup) pq[hash].push(p);
	return ret;
}

int riseCosts(int hid)
{
	int ret = 0;
	for (int rid : roomList[hid])
	{
		int hash = room[rid].hash, & price = room[rid].price;

		price += price / 10;
		pq[hash].push({ price, rid });

		ret += price;
	}
	return ret;
}
#endif // 1 // 200 ms
```
