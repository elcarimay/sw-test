```cpp
#if 1
#include <unordered_map>
#include <unordered_set>
#include <vector>
using namespace std;

#define MAXN 10003

unordered_set<int> sub[MAXN];
unordered_map<int, int> idMap;
int pa[MAXN], idCnt, cnt, infect[MAXN], curinfect[MAXN], total; // current infect

void init() {
	for (int i = 1; i <= idCnt; i++) sub[i].clear();
	idCnt = 1, idMap.clear();
	idMap[10000] = idCnt++;
	total = cnt = 0;
}

int getID(int c) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

int delta;
int dfs(int x, int cmd = 0) {
	if (cmd == 1 && infect[x]) curinfect[x] += delta, total += delta; // 감염
	if (cmd == 2 && infect[x]) total += (infect[x] - curinfect[x]), curinfect[x] = infect[x]; // 회복
	if (cmd == 3 && infect[x]) total -= curinfect[x], cnt--; // 제거
	int sum1 = curinfect[x];
	for (auto y : sub[x]) sum1 += dfs(y, cmd);
	return sum1;
}

int cmdAdd(int newID, int pID, int fileSize) { // fileSize가 0이면, 디렉토리, 0아니면 파일의미함.
	int nid = getID(newID), pid = getID(pID);
	sub[pid].insert(nid);
	curinfect[nid] = infect[nid] = fileSize;
	pa[nid] = pid;
	total += fileSize;
	if (fileSize) cnt++;
	return dfs(pid);
}

int cmdMove(int tID, int pID) {
	int nid = getID(tID), pid = getID(pID);
	sub[pid].insert(nid);
	sub[pa[nid]].erase(nid);
	pa[nid] = pid;
	return dfs(pid);
}

int cmdInfect(int tID) {
	if (cnt) delta = total / cnt;
	return dfs(getID(tID), 1);
}

int cmdRecover(int tID) {
	return dfs(getID(tID), 2);
}

int cmdRemove(int tID) {
	int id = getID(tID);
	sub[pa[id]].erase(id);
	return dfs(id, 3);
}
#endif // 1

```
