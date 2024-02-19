```cpp
#if 1
#define MAX_EMPLOYEE 8000
#include <queue>
#include <unordered_map>
using namespace std;

struct Employee
{
	int id, start, end;
	bool isRemoved;
};

Employee employee[MAX_EMPLOYEE];
unordered_map<int, int> employeeMap;
int employeeRowCnt;
int removedEmployeeRowCnt;

void init() {
	employeeMap.clear();
	employeeRowCnt = removedEmployeeRowCnt = 0;
	for (int i = 0; i < MAX_EMPLOYEE; i++) employee[i] = {};
}

int getEmployeeRownum(int id) {
	auto iter = employeeMap.find(id);
	if (iter == employeeMap.end())
		return -1;
	return iter->second;
}

int add(int mId, int mStart, int mEnd) {
	int rownum = getEmployeeRownum(mId);
	if (rownum == -1) {
		rownum = employeeRowCnt;
		employeeMap[mId] = rownum;
		employeeRowCnt++;
	}
	else if (employee[rownum].isRemoved)
		removedEmployeeRowCnt--;
	employee[rownum] = { mId, mStart, mEnd, false };
	return employeeRowCnt - removedEmployeeRowCnt;
}

int remove(int mId) {
	int rownum = getEmployeeRownum(mId);
	if (!employee[rownum].isRemoved) {
		employee[rownum].isRemoved = true;
		removedEmployeeRowCnt++;
	}
	return employeeRowCnt - removedEmployeeRowCnt;
}

int announce(int mDuration, int M) {
	priority_queue<pair<int, int>> clockIn;
	priority_queue<pair<int, int>> clockOut;

	for (int i = 0; i < employeeRowCnt; i++)
	{
		if (employee[i].isRemoved) continue;
		clockIn.push({ -employee[i].start, i });
	}

	while (!clockIn.empty()) {
		int rownum = clockIn.top().second;
		int i = -clockIn.top().first;
		int endtime = -clockIn.top().first + mDuration - 1;

		clockIn.pop();
		clockOut.push({ -employee[rownum].end, rownum });

		// 출근한 임직원들 중에서 방송 끝까지 못듣고 퇴근할 사람을 찾아서 제거한다.
		// [가장 늦은 출근자 출근시각] <= [방송시간] <= [출근시간 + mDuration시각 - 1]
		while (!clockOut.empty() && -clockOut.top().first < endtime) clockOut.pop();
		if (clockOut.size() >= M) return i;
	}
	return -1;
}
#endif
```
