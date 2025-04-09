```cpp
#if 1
#include <unordered_map>
#include <string>
#include <utility>
using namespace std;
#define MAXN 12003
unordered_map<string, int> idMap;
int idCnt;
int getID(char c[]) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}
int p[MAXN], depth[MAXN], sq, sumA[1003], A[1000003];

void update(int l, int r) {
	int s = l / sq, e = r / sq;
	for (int i = l; i <= (s + 1) * sq - 1; i++) A[i]++;
	for (int i = s + 1; i <= e - 1; i++) sumA[i]++;
	for (int i = e * sq; i <= r; i++) A[i]++;
}

void init(char mAncestor[], int mLastday) {
	idCnt = 0, idMap.clear();
	for (sq = 1; sq * sq < MAXN; sq++);
	memset(sumA, 0, sizeof(sumA));
	memset(A, 0, sizeof(A));
	int id = getID(mAncestor);
	p[id] = -1, depth[id] = 0;
	update(0, mLastday);
}

int add(char mName[], char mParent[], int mFirstday, int mLastday) {
	int cid = getID(mName), pid = getID(mParent);
	p[cid] = pid, depth[cid] = depth[pid] + 1;
	, period[cid] = { mFirstday, mLastday };
	return depth[cid];
}

int distance(char mName1[], char mName2[]) {
	int id1 = getID(mName1), id2 = getID(mName2);
	int ret = 0;
	if (depth[id1] < depth[id2]) swap(id1, id2);
	while (depth[id1] != depth[id2]) id1 = p[id1], ret++;
	while (id1 != id2) id1 = p[id1], id2 = p[id2], ret += 2;
	return ret;
}

int count(int mDay) {

	return 0;
}
#endif // 1

```
