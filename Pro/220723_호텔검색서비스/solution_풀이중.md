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
	for (int i = 0; i < roomCnt; i++){
		if (hMap[rid.rid] == hash_val) {

			if (out <= nx.check_in || in >= nx.check_out) {
				return nx.rid;
			}
			else if (nx.check_in == 0 && nx.check_out == 0) {
				return nx.rid;
			}
		}
	}
	return -1;
}

int riseCosts(int mHotelID){
	for (auto nx : hotel[mHotelID]) {
		nx
	}
	return 0;
}
#endif // 1
```
