```cpp
#if 1
#include <set>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <string>
using namespace std;

#define MAXN 6003

unordered_map<string, int> uMap, pMap;
unordered_map<int, int> attempt[50003];
int uCnt, pCnt;
int total;
struct User {
	int pass, fail;
	int cr, br, wr; // current_rank, best_rank, worst_rank
}user[MAXN];

int score[50000];

struct Result {
	int current_rank;
	int best_rank;
	int worst_rank;
};

void init() {
	uCnt = pCnt = total = 0, uMap.clear(), pMap.clear();
	memset(score, 0, sizeof(score));
	for (int i = 0; i < MAXN; i++) user[i] = {};
	for (int i = 0; i < 50000; i++) attempt[i].clear();
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

void newProblem(char mProblemName[], int mScore) {
	score[pgetID(mProblemName)] = mScore;
	total += mScore;
}

void changeProblemScore(char mProblemName[], int mNewScore) {
	int pid = pgetID(mProblemName);
	for (auto p : attempt[pid]) {
		if (p.second) user[p.first].pass += mNewScore - score[pid];
		else user[p.first].fail += mNewScore - score[pid];
	}
	total += mNewScore - score[pid];
	score[pid] = mNewScore;
}

void attemptProblem(char mPlayerName[], char mProblemName[], int mAttemptResult) {
	int uid = ugetID(mPlayerName), pid = pgetID(mProblemName);
	attempt[pid][uid] = mAttemptResult;
	mAttemptResult ? user[uid].pass += score[pid] : user[uid].fail += score[pid];
}

Result getRank(char mPlayerName[]) {
	Result res = { -1, -1, -1 };
	int id = ugetID(mPlayerName);
	user[id].cr = user[id].br = user[id].wr = 1;
	for (int i = 0; i < uCnt; i++) {
		if (i == id) continue;
		if (user[id].pass < user[i].pass) user[id].cr++;
		if (total - user[id].fail < user[i].pass) user[id].br++;
		if (user[id].pass < total - user[i].fail) user[id].wr++;
	}
	return { user[id].cr, user[id].br, user[id].wr };
}
#endif // 1
```
