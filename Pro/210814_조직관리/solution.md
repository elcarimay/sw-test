```cpp
// 합격률 3.7% (14/370)
#include <unordered_map>
#include <unordered_set>
#include <set>
#include <string>
#include <iostream>
using namespace std;

struct Department {
	int id, cnt, parent;
	bool operator<(const Department& r)const {
		if (cnt != r.cnt) return cnt < r.cnt;
		return id > r.id;
	}
}department[10003];

unordered_set<int> sub[10003]; // 정렬되어 있지 않아도 바로 지울수 있기 때문에 unordered_set을 사용함
unordered_map<string, int> hMap;
unordered_map<int, string> rhMap;
int hCnt;

void init(int mNum){
	for (int i = 0; i <= hCnt; i++) sub[i].clear();
	hMap.clear();
	hMap["root"] = hCnt = 1; rhMap[1] = "root";
	department[1] = { 1, mNum, 0 };
}

void destroy(){}

int getID(char c[]) {
	return hMap.count(c) ? hMap[c] : 0;
}

int getDepth(int id) {
	int depth = 0;
	for (int i = id; i != 1; i = department[i].parent) depth++;
	return depth;
}

int addDept(char mUpperDept[], char mNewDept[], int mNum){
	int oid = getID(mUpperDept), nid = getID(mNewDept);
	if (!oid || nid) return -1;
	if (sub[oid].size() >= 10 || department[oid].cnt <= mNum ||
		getDepth(oid) >= 5) return -1;
	hMap[mNewDept] = nid = ++hCnt; rhMap[nid] = mNewDept;
	sub[oid].insert(nid);
	department[oid].cnt -= mNum;
	department[nid] = { nid, mNum, oid };
	return department[oid].cnt;
}

int getFar(int x) { // 하부조직은 여러개가 있기 때문에 최대값을 찾음.
	cout << "x: " << rhMap[x] << endl;
	int cnt = 0;
	for (auto y : sub[x]) cnt = max(cnt, getFar(y));
	return cnt + 1;
}

int mergeDept(char mDept1[], char mDept2[]){
	int id1 = getID(mDept1), id2 = getID(mDept2);
	if (!id1 || !id2) return -1;
	if (sub[id1].size() + sub[id2].size() - (department[id2].parent == id1) > 10) return -1; // id2의 상위부서가 id1인 경우도 따져야 함
	int a = getDepth(id1);
	int b = getFar(id2);
	cout << endl;
	if (a + b - 1 > 5) return -1;
	for (int x = id1; x; x = department[x].parent) if (x == id2) return -1;
	department[id1].cnt += department[id2].cnt;
	for (auto x : sub[id2]) sub[id1].insert(x), department[x].parent = id1;
	sub[department[id2].parent].erase(id2);
	sub[id2].clear();
	hMap.erase(mDept2);
	department[id2].id = 0;
	return department[id1].cnt;
}

int oid, moveNum;
void move(int x, int depth) {
	if (!depth) return;
	for (auto y : sub[x]) {
		int num = min(moveNum, department[y].cnt - 1); // 최소 한명은 남겨야 함
		department[oid].cnt += num;
		department[y].cnt -= num;
		move(y, depth - 1);
	}
}

int moveEmployee(char mDept[], int mDepth, int mNum){
	oid = getID(mDept), moveNum = mNum;
	if (!oid) return -1;
	move(oid, mDepth);
	return department[oid].cnt;
}

void recruit(int mDeptNum, int mNum){
	set<Department> s;
	for (int i = 1; i <= hCnt; i++) {
		if (!department[i].id) continue;
		s.insert(department[i]);
		if (s.size() > mDeptNum) s.erase(--s.end());
	}
	for (auto& x : s) department[x.id].cnt += mNum;
}
```
