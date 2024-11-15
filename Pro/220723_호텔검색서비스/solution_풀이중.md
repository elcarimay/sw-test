```cpp
#if 1
#include <set>
#include <unordered_map>
using namespace std;

struct Data {
	int rid, cost;
	bool operator<(const Data& r)const {
		if (cost != r.cost) return cost < r.cost;
		return rid < r.rid;
	}
};

struct Room {
	int check_in, check_out;
	set<Data>::iterator it;
}room[100003];
int roomCnt;

int hash_fun(int info[]) {
	int ret = 0;
	for (int i = 0; i < 4; i++)
		if (i < 2)	ret += info[i] * 100;
		else ret += info[i] * 10;
	return ret;
}

unordered_map<int, int> rMap; // rid, roomCnt
unordered_map<int, int> hMap; // rid, hash_value
set<Data> r;
vector<int> hotel[1003]; // rid

int N;
void init(int N, int mRoomCnt[]){
	::N = N; rMap.clear(); hMap.clear(); roomCnt = 0;
}

void addRoom(int mHotelID, int mRoomID, int mRoomInfo[]){
	rMap[mRoomID] = roomCnt;
	hMap[mRoomID] = hash_fun(mRoomInfo);
	room[roomCnt++] = { 0,0,r.insert({ mRoomID, mRoomInfo[4]}).first };
	hotel[mHotelID].push_back(mRoomID);
}

int findRoom(int mFilter[]){
	int tmp[4];
	for (int i = 0; i < 4; i++) tmp[i] = mFilter[i + 2];
	int hash_val = hash_fun(tmp);
	int in = mFilter[0], out = mFilter[1];
	for (auto nx:r){
		if (hMap[nx.rid] == hash_val) {
			if (out <= room[rMap[nx.rid]].check_in || in >= room[rMap[nx.rid]].check_in) {
				return nx.rid;
			}
			else if (room[rMap[nx.rid]].check_in && room[rMap[nx.rid]].check_in) {
				return nx.rid;
			}
		}
	}
	return -1;
}

int riseCosts(int mHotelID){
	int sum = 0;
	for (int rid : hotel[mHotelID]) {
		int rcnt = rMap[rid];
		int cost = room[rcnt].it->cost;
		cost *= 1.1;
		r.erase(room[rcnt].it);
		room[rcnt].it = r.insert({ rid,cost}).first;
		sum += cost;
	}
	return sum;
}
#endif // 1
```
