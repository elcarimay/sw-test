```cpp
// 대기열에 대한 벡터나 set을 안씀
#include <vector>
#include <string>
#include <unordered_map>
using namespace std;

#define LOGINED 0
#define WAINTING 1

unordered_map<string, int> idMap;
unordered_map<string, vector<int>> p3, p4, p5;
int idCnt, order[50003], state[50003]; // login 0, waiting 1
struct Data {
	int id, order;
};
Data que[50000]; int head, tail;

void init() {
	idMap.clear(); idCnt = head = tail = 0;
	p3.clear(); p4.clear(); p5.clear();
	for (int i = 0; i < 50000; i++) state[i] = {};
}

void loginID(char mID[10]) {
	int id;
	if (idMap.count(mID) == 0) id = idMap[mID] = idCnt++;
	else id = idMap[mID];
	que[tail] = { id, order[id] = tail }; tail++;
	state[id] = WAINTING;
	char t3[4], t4[5], t5[6];
	t3[3] = '\0', t4[4] = '\0', t5[5] = '\0';
	for (int i = 0; i < 5; i++)
		if (i < 3) t3[i] = mID[i], t4[i] = mID[i], t5[i] = mID[i];
		else if (i < 4) t4[i] = mID[i], t5[i] = mID[i];
		else t5[i] = mID[i];
	p3[t3].push_back(id); p4[t4].push_back(id); p5[t5].push_back(id);
}

int closeIDs(char mStr[10]) {
	int cnt = 0, len = strlen(mStr);
	if (len == 3) {
		for (int i = 0; i < p3[mStr].size(); i++)
			if (state[p3[mStr][i]] == WAINTING) {
				state[p3[mStr][i]] = LOGINED, cnt++;
			}
	}
	else if (len == 4) {
		for (int i = 0; i < p4[mStr].size(); i++)
			if (state[p4[mStr][i]] == WAINTING) {
				state[p4[mStr][i]] = LOGINED, cnt++;
			}
	}
	else {
		for (int i = 0; i < p5[mStr].size(); i++)
			if (state[p5[mStr][i]] == WAINTING) {
				state[p5[mStr][i]] = LOGINED, cnt++;
			}
	}
	return cnt;
}

void connectCnt(int mCnt) {
	int old_head = head, id, oCnt;
	while (head < tail) {
		if (mCnt == 0) {
			head = old_head; return;
		}
		id = que[head].id, oCnt = que[head++].order;
		if (state[id] == WAINTING && oCnt == order[id]) {
			state[id] = LOGINED; mCnt--;
		}
	}
}

int waitOrder(char mID[10]) {
	if (idMap.count(mID) == 0) return 0;
	int id = idMap[mID];
	if (state[id] == LOGINED) return 0;
	int old_head = head, id2, cnt = 0, oCnt;
	while (head < tail) {
		id2 = que[head].id, oCnt = que[head++].order;
		if (state[id2] == WAINTING && oCnt == order[id2]) {
			cnt++;
			if (id2 == id) {
				head = old_head;
				return cnt;
			}
		}
	}
}
```
