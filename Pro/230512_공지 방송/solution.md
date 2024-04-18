```cpp
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
unordered_map<int, int> employeeMap; // id, rownum
int employeeRowCnt;
int removedEmployeeRowCnt;

void init() {
	employeeMap.clear();
	employeeRowCnt = removedEmployeeRowCnt = 0;
	for (int i = 0;i < MAX_EMPLOYEE;i++) employee[i] = {};
	return;
}

int getEmployeeRownum(int id) {
	auto iter = employeeMap.find(id);
	if (iter == employeeMap.end()) return -1;
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

struct Data
{
	int time, rownum;
	bool operator<(const Data& data)const {
		return time > data.time;
	}
};

int announce(int mDuration, int M) {
	priority_queue<Data> clockIn;
	priority_queue<Data> clockOut;
	for (int i = 0;i < employeeRowCnt;i++) {
		if (employee[i].isRemoved) continue;
		clockIn.push({ employee[i].start,i }); // 시작시간, rownum
	}
	while (!clockIn.empty()) {
		int rownum = clockIn.top().rownum;
		int i = clockIn.top().time;
		int endtime = clockIn.top().time + mDuration - 1;
		clockIn.pop();
		clockOut.push({ employee[rownum].end, rownum });
		while (!clockOut.empty() && clockOut.top().time < endtime) clockOut.pop();
		if (clockOut.size() >= M) return i;
	}
	return -1;
}
#endif
