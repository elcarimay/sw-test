```cpp
#if 0 // 풀이중
#define _CRT_SECURE_NO_WARNINGS
#include <set>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

static const int MAXL = 16;
struct RESULT {
	char mApp[5][MAXL];
}res;

struct DB {
	int sum, avg, cnt;
	char c[MAXL];
}db[10003];

unordered_map<string, int> aMap;
int aCnt;
bool state[10003]; // false: normal, true: ban

int getID(const char c[]) {
	auto it = aMap.find(c);
	int id;
	if (it != aMap.end()) id = aMap[c];
	return id = aMap[c] = aCnt++;
}

struct Data1 {
	int aid;
	bool operator<(const Data1& r)const {
		if (db[aid].avg != db[r.aid].avg) return db[aid].avg > db[r.aid].avg;
		bool flag;
		strcmp(db[aid].c, db[r.aid].c) < 0 ? flag = true : flag = false;
		return flag;
	}
};

struct Data2 {
	int aid;
	bool operator<(const Data2& r)const {
		if (db[aid].cnt != db[r.aid].cnt) return db[aid].cnt > db[r.aid].cnt;
		bool flag;
		strcmp(db[aid].c, db[r.aid].c) < 0 ? flag = true : flag = false;
		return flag;
	}
};

unordered_map<int, int> eval[10003]; // user별 aid, score
set<Data1> score;
set<Data2> number;
set<Data1>::iterator sit[10003];
set<Data2>::iterator nit[10003];


void insert(int aid) {
	sit[aid] = score.insert({ aid }).first;
	nit[aid] = number.insert({ aid }).first;
}

void update(int aid) {
	score.erase({ aid });
	number.erase({ aid });
	insert(aid);
}

void init(int N, const char mApp[][MAXL]){
	aCnt = 0, aMap.clear(), score.clear(), number.clear();
	for (int i = 0; i < 10000; i++)	db[i] = {}, state[i] = false, eval[i].clear();
	for (int i = 0; i < N; i++) {
		int aid = getID(mApp[i]);
		strcpy(db[aid].c, mApp[i]);
		insert(aid);
	}
}

void addRating(int mUser, const char mApp[MAXL], int mScore){
	if (!state[mUser]) return;
	int aid = getID(mApp);
	if (eval[mUser].count(aid)) {
		db[aid].sum -= eval[mUser][aid], db[aid].cnt--;
		if (db[aid].cnt) db[aid].avg = db[aid].sum * 10 / db[aid].cnt;
	}
	db[aid].sum += mScore, db[aid].cnt++;
	if(db[aid].cnt) db[aid].avg = db[aid].sum * 10 / db[aid].cnt;
	eval[mUser][aid] = mScore;
	update(aid);
}

void deleteRating(int mUser, const char mApp[MAXL]){
	int aid = getID(mApp);
	db[aid].sum -= eval[mUser][aid], db[aid].cnt--;
	if (db[aid].cnt) db[aid].avg = db[aid].sum * 10 / db[aid].cnt;
	update(aid);
	eval[mUser].erase(aid);
}

void banUser(int mUser){
	if (state[mUser]) return;
	state[mUser] = true;
	for (auto d : eval[mUser]) {
		int aid = d.first, score = d.second;
		db[aid].sum -= eval[mUser][aid], db[aid].cnt--;
		if (db[aid].cnt) db[aid].avg = db[aid].sum * 10 / db[aid].cnt;
		update(aid);
	}
}

RESULT sortByScore() {
	auto it = score.begin();
	for (int i = 0; i < 5; i++, ++it) strcpy(res.mApp[i], db[it->aid].c);
	return res;
}

RESULT sortByNumber() {
	auto it = number.begin();
	for (int i = 0; i < 5; i++, ++it) strcpy(res.mApp[i], db[it->aid].c);
	return res;
}
#endif // 1

```
