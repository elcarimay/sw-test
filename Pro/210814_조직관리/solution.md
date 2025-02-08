```cpp
#include <unordered_map>
#include <unordered_set>
#include <set>
#include <string>
using namespace std;

struct Department {
	int id, cnt, parent;
	bool operator<(const Department& r)const {
		if (cnt != r.cnt) return cnt < r.cnt;
		return id > r.id;
	}
}department[10003];
unordered_set<int> sub[10003];
int hcnt;
unordered_map<string, int> htab;
void init(int mNum){
	for (int i = 0; i <= hcnt; i++) sub[i].clear();
	htab.clear();
	htab["root"] = hcnt = 1;
	department[1] = { 1, mNum, 0 };
}

void destroy(){}

int getID(char str[]) {
	return htab.count(str) ? htab[str] : 0;
}

int getDepth(int id) {
	int depth = 0;
	for (int x = id; x != 1; x = department[x].parent) depth++;
	return depth;
}

int addDept(char mUpperDept[], char mNewDept[], int mNum){
	int pid = getID(mUpperDept), uid = getID(mNewDept);
	if (!pid || uid) return -1;
	if (sub[pid].size() >= 10 || department[pid].cnt <= mNum ||
		getDepth(pid) >= 5) return -1;
	htab[mNewDept] = uid = ++hcnt;
	sub[pid].insert(uid);
	department[pid].cnt -= mNum;
	department[uid] = { uid, mNum, pid };
	return department[pid].cnt;
}

int getFar(int x) {
	int cnt = 0;
	for (auto y : sub[x]) cnt = max(cnt, getFar(y));
	return cnt + 1;
}

int mergeDept(char mDept1[], char mDept2[]){
	int id1 = getID(mDept1), id2 = getID(mDept2);
	if (!id1 || !id2) return -1;
	if (sub[id1].size() + sub[id2].size() - (department[id2].parent == id1) > 10)
		return -1;
	if (getDepth(id1) + getFar(id2) - 1 > 5) return -1;
	for (int x = id1; x; x = department[x].parent)
		if (x == id2) return -1;
	department[id1].cnt += department[id2].cnt;
	for (auto x : sub[id2]) sub[id1].insert(x), department[x].parent = id1;
	sub[department[id2].parent].erase(id2);
	sub[id2].clear();
	htab.erase(mDept2);
	department[id2].id = 0;
	return department[id1].cnt;
}

int uid, moveNum;
void move(int x, int depth) {
	if (!depth) return;
	for (auto y : sub[x]) {
		int num = min(moveNum, department[y].cnt - 1);
		department[uid].cnt += num;
		department[y].cnt -= num;
		move(y, depth - 1);
	}
}

int moveEmployee(char mDept[], int mDepth, int mNum){
	uid = getID(mDept), moveNum = mNum;
	if (!uid) return -1;
	move(uid, mDepth);
	return department[uid].cnt;
}

void recruit(int mDeptNum, int mNum){
	set<Department> s;
	for (int i = 1; i <= hcnt; i++) {
		if (!department[i].id) continue;
		s.insert(department[i]);
		if (s.size() > mDeptNum) s.erase(--s.end());
	}
	for (auto& x : s) department[x.id].cnt += mNum;
}
```
