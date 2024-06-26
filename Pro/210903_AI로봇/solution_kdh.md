```cpp
#include <queue>
#include <vector>
#include <string.h>
using namespace std;
using pii = pair<int, int>; // -inteligence, id
using pbb = pair<bool, bool>;

#define MAX_N 50005

pii robot[MAX_N]; // inteligence, jid
priority_queue<pii> maxpq; // {inteligence, -id}높은게 위로
priority_queue<pii, vector<pii>, greater<>> minpq; // {-inteligence, id}낮은게 위로
vector<int> job[MAX_N];
pbb checked[MAX_N]; // broken, working 상태확인
bool tc[MAX_N]; // trainning center

int N, currentTime;
void init(int N){
	::N = N, currentTime = 0;
	for (int i = 1; i < MAX_N; i++)
		job[i].clear(), robot[i] = {}, checked[i] = {}, tc[i] = 0;
	maxpq = {}, minpq = {};
	for (int i = 1; i <= N; i++)
		maxpq.push({ 0,-i }), minpq.push({ 0,i }), tc[i] = 1;
}

void popNum(int num, vector<int>& v, int op) {
	int intel, id;
	while(1){
		if (op) {
			intel = minpq.top().first;
			id = minpq.top().second;
		}
		else {
			intel = maxpq.top().first;
			id = -maxpq.top().second;
		}
		maxpq.pop(); minpq.pop();
		if (!tc[id]) continue;
		if (!checked[id].first && !checked[id].second && intel == robot[id].first) {
			v.push_back(id);
		}
		if (v.size() == num) break;
	}
}

void update(int cTime) {
	for (int i = 1; i <= N; i++) {
		if (tc[i]) {
			robot[i].first += (cTime - currentTime);
			minpq.push({ robot[i].first, i });
			maxpq.push({ robot[i].first, -i });
		}
	}
}

int callJob(int cTime, int wID, int mNum, int mOpt){
	update(cTime);
	vector<int> list; int ret = 0;
	popNum(mNum, list, mOpt);
	for (int i = 0; i < list.size(); i++) {
		ret += list[i];
		job[wID].push_back(list[i]);
		checked[list[i]].second = 1;
		robot[list[i]].second = wID;
		tc[list[i]] = 0;
	}
	currentTime = cTime;
	return ret;
}

void returnJob(int cTime, int wID){
	update(cTime);
	for (int i = 0; i < job[wID].size(); i++) {
		checked[job[wID][i]].second = 0;
		minpq.push({ robot[job[wID][i]].first, job[wID][i] });
		maxpq.push({ robot[job[wID][i]].first, -job[wID][i] });
		tc[job[wID][i]] = 1;
	}
	job[wID].clear();
	currentTime = cTime;
}

void broken(int cTime, int rID){
	update(cTime);
	currentTime = cTime;
	if (tc[rID]) return;
	if (!checked[rID].first && checked[rID].second) {
		checked[rID].first = 1;
		checked[rID].second = 0;
		tc[rID] = 1;
		for (int i = 0; i < job[robot[rID].second].size(); i++) {
			if (job[robot[rID].second][i] == rID)
				job[robot[rID].second].erase(job[robot[rID].second].begin() + i);
		}
	}
}

void repair(int cTime, int rID){
	update(cTime);
	currentTime = cTime;
	if (checked[rID].first) {
		checked[rID].first = 0;
		robot[rID].first = 0;
		minpq.push({ 0, rID });
		maxpq.push({ 0, -rID });
		currentTime = cTime;
		tc[rID] = 1;
	}
}

int check(int cTime, int rID){
	update(cTime);
	currentTime = cTime;
	if (checked[rID].first) return 0;
	if (checked[rID].second) return -robot[rID].second;
	else return robot[rID].first;
}
```
