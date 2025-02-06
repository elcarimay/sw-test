```cpp
#include <set>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <string>
using namespace std;

#define MAXN 6003

unordered_map<string, int> uMap, pMap;
int uCnt, pCnt;


struct Data{
	int id, score;
	bool operator<(const Data& r)const {
		return score > r.score;
	}
};
set<Data> current;
set<Data> maxmin;
set<Data>::iterator it[MAXN][2]; // 0: 현재, 1: 최선

struct User {
	int current, rest;
}user[MAXN];

struct DB {
	int score;
	bool attempt[MAXN];
	bool success[MAXN];
}db[50000];

struct Result {
	int current_rank;
	int best_rank;
	int worst_rank;
};

void init() {
	uCnt = pCnt = 0, uMap.clear(), pMap.clear();
	memset(db, 0, sizeof(db));
	current.clear(), maxmin.clear();
	for (int i = 0; i < 50000; i++) db[i] = {};
}

void destroy() {}

int ugetID(char c[]) {
	int id;
	unordered_map<string, int>::iterator it = uMap.find(c);
	if (it != uMap.end()) id = uMap[c];
	else id = uMap[c] = uCnt++;
	return id;
}

int pgetID(char c[]) {
	int id;
	unordered_map<string, int>::iterator it = pMap.find(c);
	if (it != pMap.end()) id = pMap[c];
	else id = pMap[c] = pCnt++;
	return id;
}

void newPlayer(char mPlayerName[]) {
	ugetID(mPlayerName);
}

void update() {
	for (int i = 0; i < uCnt; i++) {
		for (int j = 0; j < pCnt; j++) {
			if (db[j].attempt[i]) {
				if (db[j].success[i]) user[i].current += db[j].score;
				else user[i].rest += db[j].score;
			}
		}
		it[i][0] = current.insert({ i, user[i].current }).first;
		it[i][1] = maxmin.insert({ i, user[i].current + user[i].rest}).first;
	}
}

void newProblem(char mProblemName[], int mScore) {
	db[pgetID(mProblemName)].score = mScore;
}

void changeProblemScore(char mProblemName[], int mNewScore) {
	newProblem(mProblemName, mNewScore);
}

void attemptProblem(char mPlayerName[], char mProblemName[], int mAttemptResult) {
	int uid = ugetID(mPlayerName), pid = pgetID(mProblemName);
	db[pid].attempt[uid] = true;
	mAttemptResult ? db[pid].success[uid] = true : db[pid].success[uid] = false;
}

Result getRank(char mPlayerName[]) {
	Result res = { -1, -1, -1 };
	int id = ugetID(mPlayerName);


	return res;
}
```
