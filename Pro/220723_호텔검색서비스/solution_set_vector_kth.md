```cpp
#if 1 // 204 ms
/*
* 방 형태별 분류 : set
* 방 예약시간 관리 : vector, linear search
*/
#include<vector>
#include<set>
using namespace std;
using pii = pair<int, int>;

vector<int> roomList[1003];
struct RoomInfo
{
	int hash, price;
	set<pii>::iterator it;
	vector<pii> date;
}room[100003];

set<pii> S[10003];


void init(int N, int mRoomCnt[])
{
	for (int i = 1; i <= N; i++) roomList[i].clear();
	for (auto& s : S) s.clear();
}

int gethash(int* a) { return (a[0] - 1) * 1000 + (a[1] - 1) * 100 + a[2] * 10 + a[3]; }

void addRoom(int hid, int rid, int info[])
{
	roomList[hid].push_back(rid);
	int hash = gethash(info), price = info[4];
	room[rid] = { hash, price, S[hash].insert({ price, rid }).first };
}

bool overlap(int rid, int s, int e)	// 겹치는 구간 있으면 1 , 없으면 0 반환
{
	for (pii d : room[rid].date)
		if ((d.second <= s || e <= d.first) == 0) return 1;
	return 0;
}

int findRoom(int filter[])
{
	int s = filter[0], e = filter[1], hash = gethash(filter + 2);

	for (pii p : S[hash])
	{
		int rid = p.second;
		if (overlap(rid, s, e)) continue;
		room[rid].date.push_back({ s,e });
		return rid;
	}
	return -1;
}

int riseCosts(int hid)
{
	int ret = 0;
	for (int rid : roomList[hid])
	{
		int hash = room[rid].hash, & price = room[rid].price;
		auto& it = room[rid].it;

		price += price / 10;
		S[hash].erase(it);
		it = S[hash].insert({ price, rid }).first;

		ret += price;
	}
	return ret;
}
#endif // 1 // 204 ms

```
