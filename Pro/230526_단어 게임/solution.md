```cpp
#define _CRT_SECURE_NO_WARNINGS
#define MAXL 10
#define MAX_N 10005

#include <vector>
#include <string.h>
#include <string>
#include <unordered_map>
using namespace std;

int N;
struct Card {
	char word[MAXL];
	int cnt;
}card[MAX_N];

bool comp(int a, int b) {
	if (card[a].cnt != card[b].cnt) return card[a].cnt < card[b].cnt;
	return strcmp(card[a].word, card[b].word) > 0;
}

int M;
struct Player {
	int score, leave;
	int cnt[MAX_N];
	int pickCard(vector<int>& v) {
		int res = 0;
		if (!leave)
			for (int cid : v)
				if (cnt[cid] && comp(res, cid)) res = cid;
		return res;
	}
}player[55];

unordered_map<string, vector<int>> hmap;

void init(int n, char mWord[][11], char mSubject[][11]) {
	hmap.clear();
	N = n, M = 0;
	memset(player, 0, sizeof(player));
	for (int i = 1; i <= N; i++) {
		strcpy(card[i].word, mWord[i - 1]);
		card[i].cnt = 0;
		
		string s;
		for (int j = 0; j < 2; j++) {
			s += mWord[i - 1][j];
			hmap[s + "_" + mSubject[i - 1]].push_back(i);
		}
	}
}

void join(int pid, int m, int mCard[]) {
	M++;
	for (int i = 0; i < m; i++)
		if (++player[pid].cnt[mCard[i]] == 1) card[mCard[i]].cnt++; // 중복으로 갖고 있어도 한명으로 치기 때문
}

int playRound(char mBeginStr[], char mSubject[]) {
	int res = 0;
	int cnt[MAX_N] = {};
	int pick[55] = {};
	auto& v = hmap[string(mBeginStr) + "_" + mSubject];

	for (int pid = 1; pid <= M; pid++) {
		int cid = player[pid].pickCard(v);
		if (cid) {
			res += cid;
			pick[pid] = cid;
			cnt[cid]++;
		}
	}

	for (int pid = 1; pid <= M; pid++) {
		int cid = pick[pid];
		if (cid) {
			player[pid].score += (cnt[cid] - 1) * (cnt[cid] - 1);
			if (--player[pid].cnt[cid] == 0) card[cid].cnt--; // 한개 갖고 있을때만 card cnt에서 제거
		}
	}
	return res;
}

int leave(int pid) {
	player[pid].leave = 1;
	for (int i = 1; i <= N; i++)
		if (player[pid].cnt[i]) card[i].cnt--;
	return player[pid].score;
}
```
