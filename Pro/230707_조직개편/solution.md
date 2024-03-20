```cpp
#if 1
#include <unordered_map>
using namespace std;

#define MAX_DEPT 8000

struct Department
{
	int mId, num, pId, sum, cIds[2], subDeptCnt;
	void add(int deptId) {
		if (cIds[0] == -1) cIds[0] = deptId;
		else cIds[1] = deptId;
		subDeptCnt++;
	}
	void remove(int deptId) {
		if (cIds[0] == deptId) cIds[0] = -1;
		else cIds[1] = -1;
		subDeptCnt--;
	}
}department[MAX_DEPT];

unordered_map<int, int> deptMap;
int deptCnt, maxSum, grpCnt;

int newDepartment(int mId, int num, int pId) {
	department[deptCnt] = { mId, num, pId, num, {-1, -1}, 0 };
	deptMap.emplace(mId, deptCnt);
	return deptCnt++;
}

void init(int mId, int mNum) {
	deptMap.clear(); deptCnt = 0;
	newDepartment(mId, mNum, -1);
}

int getSum(int deptId) {
	if (deptId == -1) return 0;
	int sum = department[deptId].num;
	sum += getSum(department[deptId].cIds[0]);
	sum += getSum(department[deptId].cIds[1]);
	return sum;
}

void updateSum(int pId, int num) {
	while (pId != -1) {
		department[pId].sum += num;
		pId = department[pId].pId;
	}
}

void erase(int deptId) {
	if (deptId == -1) return;
	deptMap.erase(department[deptId].mId);
	erase(department[deptId].cIds[0]);
	erase(department[deptId].cIds[1]);
}

int add(int mId, int mNum, int mParent) {
	int pId = deptMap.find(mParent)->second;
	if (department[pId].subDeptCnt == 2) return -1;
	int newDeptId = newDepartment(mId, mNum, pId);
	department[pId].add(newDeptId);
	return getSum(pId);
}

int remove(int mId) {
	auto cur = deptMap.find(mId);
	if (cur == deptMap.end()) return -1;
	int deptId = cur->second;
	int pId = department[deptId].pId;
	erase(deptId);
	department[pId].remove(deptId);
	return getSum(deptId);
}

int max(int a, int b) { return a < b ? b : a; }
int min(int a, int b) { return a > b ? b : a; }

int reorganize(int deptId) {
	if (deptId == -1) return 0;
	else if (department[deptId].num > maxSum) return -1;
	int cId1Cnt = reorganize(department[deptId].cIds[0]);
	int cId2Cnt = reorganize(department[deptId].cIds[1]);
	if (cId1Cnt == -1 || cId2Cnt == -1) return -1;
	int subDeptSum = cId1Cnt + cId2Cnt;
	if (department[deptId].num + subDeptSum <= maxSum)
		return department[deptId].num + subDeptSum;
	else if (department[deptId].num + min(cId1Cnt, cId2Cnt) <= maxSum) {
		grpCnt++;
		return department[deptId].num + min(cId1Cnt, cId2Cnt);
	}
	grpCnt += 2;
	return department[deptId].num;
}

int reorganize(int M, int K) {
	grpCnt = 1;
	maxSum = K;
	if (reorganize(0) == -1 || grpCnt > M) return 0;
	return 1;
}
#endif // 1

```
