```cpp
#if 1
#include <unordered_map>
#include <string>
using namespace std;
#define MAXN 12003
unordered_map<string, int> idMap;
int idCnt, p[MAXN], depth[MAXN], sq, sumA[1003], A[1000003];

void update(int l, int r) {
	int s = l / sq, e = r / sq;
	if (s == e) {
		for (int i = l; i <= r; i++) A[i]++; return;
	}
	for (int i = l; i <= (s + 1) * sq - 1; i++) A[i]++;
	for (int i = s + 1; i <= e - 1; i++) sumA[i]++;
	for (int i = e * sq; i <= r; i++) A[i]++;
}

void init(char mAncestor[], int mLastday) {
	idCnt = 0, idMap.clear();
	for (sq = 1; sq * sq < 1000003; sq++);
	memset(sumA, 0, sizeof(sumA));
	memset(A, 0, sizeof(A));
	int id = idMap[mAncestor] = idCnt++;
	update(0, mLastday);
}

int add(char mName[], char mParent[], int mFirstday, int mLastday) {
	int cid = idMap[mName] = idCnt++, pid = idMap[mParent];
	p[cid] = pid, depth[cid] = depth[pid] + 1;
	update(mFirstday, mLastday);
	return depth[cid];
}

int distance(char mName1[], char mName2[]) {
	int id1 = idMap[mName1], id2 = idMap[mName2];
	int ret = 0;
	if (depth[id1] < depth[id2]) swap(id1, id2);
	while (depth[id1] != depth[id2]) id1 = p[id1], ret++;
	while (id1 != id2) id1 = p[id1], id2 = p[id2], ret += 2;
	return ret;
}

int count(int mDay) {
	return A[mDay] + sumA[mDay / sq];
}
#endif // 1
```
