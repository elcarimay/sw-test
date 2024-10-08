```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<unordered_map>
#include<vector>
#include<string>
#include<string.h>
#include<algorithm>
using namespace std;

int mcnt, ucnt;
unordered_map<string, int> mmap, umap; // meeting name, mid(mcnt) | user name, uid(ucnt)
vector<int> U[20003], M[10003]; // 사용자: 최대 20,000명, 모든 함수 호출횟수: 최대 10,000개
char mName[10003][13]; // 회의명 mName[mid]
int S[10003], E[10003]; // 시작시간 S[mid], 끝시간 E[mid]

void init() {
	for (int i = 0; i < mcnt; i++) M[i].clear();
	for (int i = 0; i < ucnt; i++) U[i].clear();
	mcnt = ucnt = 0;
	mmap.clear(); umap.clear();
}

int getuid(char s[]) {
	if (umap.count(s)) return umap[s];
	return umap[s] = ucnt++;
}

bool isOverlap(int uid, int s, int e) {
	for (int mid : U[uid])
		if (e >= S[mid] && E[mid] >= s) return 1;
	return 0;
}

void erase(vector<int>& v, int x) {
	v.erase(find(v.begin(), v.end(), x));
}

int addMeeting(char meeting[11], int m, char memberList[10][11], int startTime, int endTime) {
	for (int i = 0; i < m; i++) {
		int uid = getuid(memberList[i]);
		if (isOverlap(uid, startTime, endTime)) continue;
		U[uid].push_back(mcnt);
		M[mcnt].push_back(uid);
	}

	if (M[mcnt].empty()) return 0;
	mmap[meeting] = mcnt;
	strcpy(mName[mcnt], meeting);
	S[mcnt] = startTime, E[mcnt] = endTime;
	return M[mcnt++].size();
}

int cancelMeeting(char meeting[11]) {
	if (!mmap.count(meeting)) return 0;
	int mid = mmap[meeting];
	for (int uid : M[mid]) erase(U[uid], mid);
	mmap.erase(meeting);
	return 1;
}

int changeMeetingMember(char meeting[11], char member[11]) {
	if (!mmap.count(meeting)) return -1;
	int mid = mmap[meeting];
	int uid = getuid(member);
	if (find(M[mid].begin(), M[mid].end(), uid) != M[mid].end()) {
		erase(M[mid], uid); erase(U[uid], mid);
		if (M[mid].empty()) mmap.erase(meeting);
		return 0;
	}
	if (isOverlap(uid, S[mid], E[mid])) return 2;

	M[mid].push_back(uid);
	U[uid].push_back(mid);
	return 1;
}

int changeMeeting(char meeting[11], int startTime, int endTime) {
	if (!mmap.count(meeting)) return 0;
	int mid = mmap[meeting];
	S[mid] = startTime, E[mid] = endTime;
	for (auto it = M[mid].begin(); it != M[mid].end();) {
		int uid = *it;
		erase(U[uid], mid);
		if (isOverlap(uid, S[mid], E[mid]))
			it = M[mid].erase(it);
		else
			U[uid].push_back(mid), ++it;
	}
	if (M[mid].empty()) mmap.erase(meeting);
	return M[mid].size();
}

void checkNextMeeting(char member[11], int timeT, char result[11]) {
	int uid = getuid(member);
	int ret = 1000;
	result[0] = 0;
	for (int mid : U[uid]) {
		if (S[mid] > timeT && S[mid] < ret)
			ret = S[mid], strcpy(result, mName[mid]);
	}
}
```
