```cpp
#if 1
// 231110_KNN 유사문제
// 우큐로 풀면 시간늘어나서 partial_sort 적용하였음
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

#define MAXN 10000
#define L 1000

struct RF {
	int freq, r, c;
}rf[50003];

unordered_map<int, int> idMap;
int idCnt, N, Limit;

vector<int> g[MAXN / L + 3][MAXN / L + 3];
void init(int N, int mLimit) {
	idCnt = 0, idMap.clear(), ::N = N, Limit = mLimit;
	for (int i = 0; i < MAXN / L + 3; i++) for (int j = 0; j < MAXN / L + 3; j++) g[i][j].clear();
}

void addRadio(int K, int mID[], int mFreq[], int mY[], int mX[]) {
	for (int i = 0; i < K; i++) {
		int id = idMap[mID[i]] = idCnt++;
		rf[id] = { mFreq[i], mY[i], mX[i] };
		g[mY[i] / L][mX[i] / L].push_back(id);
	}
}

int getMinPower(int mID, int mCount) {
	vector<int> tmp;
	int org_id = idMap[mID];
	int pr = rf[org_id].r, pc = rf[org_id].c; // point
	int sr = max((pr - Limit / 10) / L, 0), er = min((pr + Limit / 10) / L, N / L);
	int sc = max((pc - Limit / 10) / L, 0), ec = min((pc + Limit / 10) / L, N / L);
	for (int r = sr; r <= er; r++) for (int c = sc; c <= ec; c++) for (int id : g[r][c]) {
		if (org_id == id) continue;
		auto& radio = rf[id];
		int power = (abs(pr - radio.r) + abs(pc - radio.c)) * 10;
		if (rf[org_id].freq != radio.freq) power += 1000;
		if (power <= Limit) tmp.push_back(power);
	}
	int ret = 0;
	partial_sort(tmp.begin(), tmp.begin() + mCount, tmp.end());
	for (int i = 0; i < mCount; i++) ret += tmp[i];
	return ret;
}
#endif // 1

```
