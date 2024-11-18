```cpp
#include <set>
#include <vector>
using namespace std;

struct Data {
	int rid, cost;
	bool operator<(const Data& r)const {
		if (cost != r.cost) return cost < r.cost;
		return rid < r.rid;
	}
};

struct Day {
	int check_in, check_out;
};

struct Room {
	int hash_value;
	vector<Day> day;
	set<Data>::iterator it;
}room[100003];
set<Data> r;
vector<int> hotel[1003]; // hid

int N;
void init(int N, int mRoomCnt[]){
	::N = N; r.clear();
	for (int i = 0; i < 1000; i++) hotel[i].clear();
	for (int i = 0; i < 100000; i++) room[i].day.clear();
}

int ret;
int hash_fun(int info[]) {
	for (int i = 0; i < 4; i++)
		if (i < 3)	ret = (ret + info[i]) * 10;
		else ret += info[i];
	return ret;
}

void addRoom(int mHotelID, int mRoomID, int mRoomInfo[]){
	ret = 0;
	room[mRoomID].hash_value = hash_fun(mRoomInfo);
	room[mRoomID].day.push_back({ 0,0 });
	room[mRoomID].it = r.insert({mRoomID, mRoomInfo[4]}).first;
	hotel[mHotelID].push_back(mRoomID);
}

int findRoom(int mFilter[]){
	int tmp[4];
	for (int i = 0; i < 4; i++) tmp[i] = mFilter[i + 2];
	ret = 0;
	int hash_val = hash_fun(tmp);
	int in = mFilter[0], out = mFilter[1];
	for (auto nx:r){
		if (room[nx.rid].hash_value == hash_val) {
			bool flag = true;
			if (room[nx.rid].day.empty()) return nx.rid;
			for (auto d : room[nx.rid].day) {
				if (!(out <= d.check_in || in >= d.check_out)) {
					flag = false;
				}
			}
			if (flag) {
				room[nx.rid].day.push_back({ in,out });
				return nx.rid;
			}
		}
	}
	return -1;
}

int riseCosts(int mHotelID){
	int sum = 0;
	for (int rid : hotel[mHotelID]) {
		int cost = room[rid].it->cost;
		cost *= 1.1;
		r.erase(room[rid].it);
		room[rid].it = r.insert({ rid,cost}).first;
		sum += cost;
	}
	return sum;
}
```
