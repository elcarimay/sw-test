```cpp
#if 1
#include <vector>
#include <algorithm>
#include <set>
#include <unordered_map>
using namespace std;

struct Result {
	int finish;
	int param;
};

int ct;
struct Data {
	int finish, id;
	bool operator<(const Data& r)const {
		if (finish != r.finish) return finish < r.finish;
		return id < r.id;
	}
};
set<Data> s;

struct Node {
	int p, speed, size, start, finish, dlCnt;
	vector<int> child;
	void update(int newSpeed) {
		size = max(0, size - (ct - start) * speed);
		if (size) {
			speed = newSpeed, start = ct;
			finish = speed ? start + (size + speed - 1) / speed : 1e9;
		}
	}
}node[50003];
void init(int mCapa) {
	ct = 0, s.clear(), node[0] = { -2, mCapa };
}

void eraseNode(int cid) {
	auto& v = node[node[cid].p].child;
	v.erase(find(v.begin(), v.end(), cid));
	node[cid].p = -1;
}

// c = 1: id인 pc추가, c = -1: id인 pc 제거
// dlCnt가 변경되는 가장 상위 노드까지 업데이트 후 해당 노드 반환
int updateDlcnt(int id, int c) {
	while (true) {
		node[id].dlCnt += c;
		if (c == 1 && node[id].dlCnt > 1) return id; // 2이상이면 
		if (c == -1 && node[id].dlCnt > 0) return id; // 
		id = node[id].p;
		if (id < 0) return 0;
	}
	return 0;
}

void updateSpeed(int id, int speed) {
	if (node[id].start) { // 1) pc인 경우
		s.erase({ node[id].finish, id }); // 기존 정보 set에서 삭제
		node[id].update(speed);
		s.insert({ node[id].finish, id });
		return;
	}
	node[id].speed = speed; // 2) hub인 경우
	for (int cid : node[id].child) // 전송중인 자식노드들에 새로운 속도 적용
		if (node[cid].dlCnt) updateSpeed(cid, speed / node[id].dlCnt);
}

void updateTime(int destTime) {
	while (!s.empty()) {
		ct = s.begin()->finish; int id = s.begin()->id;
		if (ct > destTime) break; // 가장 빨리 끝나는 시간이 목표 시간 이후인 경우
		int x = updateDlcnt(id, -1);
		s.erase(s.begin());
		eraseNode(id);
		updateSpeed(x, node[x].speed);
	}
}

void addHub(int mTime, int mParentID, int mID) {
	updateTime(mTime);
	node[mID] = { mParentID };
	node[mParentID].child.push_back(mID);
}

int removeHub(int mTime, int mID) {
	updateTime(mTime);
	if (node[mID].dlCnt) return 0;
	eraseNode(mID);
	return 1;
}

void requestDL(int mTime, int mParentID, int mpcID, int mSize) {
	updateTime(mTime);
	node[mParentID].child.push_back(mpcID);
	node[mpcID] = { mParentID, 0, mSize, mTime, mTime };

	ct = mTime;
	int x = updateDlcnt(mpcID, 1);
	updateSpeed(x, node[x].speed);
}

Result checkPC(int mTime, int mpcID) {
	Result res; res.finish = 0, res.param = 0;
	updateTime(mTime);
	ct = mTime;
	node[mpcID].update(node[mpcID].speed);
	if (node[mpcID].size) res = { 0, node[mpcID].size };
	else res = { 1, node[mpcID].finish };
	return res;
}
#endif // 1
```
