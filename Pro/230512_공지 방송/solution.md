```cpp
#define MAX_EMPOLYEE 8000
#include <queue>
#include <unordered_map>
using namespace std;

struct Employee
{
	int id, start, end;
	bool isRemoved;
}employee[MAX_EMPOLYEE];

unordered_map<int, int> Map; // id, rownum
int employeeRowCnt, removedEmployeeRowCnt;

void init() {
	Map.clear();
	employeeRowCnt = removedEmployeeRowCnt = 0;
	for (int i = 0; i < MAX_EMPOLYEE; i++) employee[i] = {};
}

int getEmployeeRownum(int id) {
	auto it = Map.find(id);
	if (it == Map.end()) return -1;
	return it->second;
}

int add(int mId, int mStart, int mEnd) {
	int rownum = getEmployeeRownum(mId);
	if (rownum == -1) {
		rownum = employeeRowCnt;
		Map[mId] = rownum;
		employeeRowCnt++;
	}
	else if(employee[rownum].isRemoved)
		removedEmployeeRowCnt--;
	employee[rownum] = { mId, mStart, mEnd };
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
	bool operator<(const Data&data)const {
		return time > data.time;
	}
};

int announce(int mDuration, int M) {
	priority_queue<Data> clockIn, clockOut;
	for (int i = 0; i < employeeRowCnt; i++) {
		if (employee[i].isRemoved) continue;
		clockIn.push({ employee[i].start, i }); // 시작시간, rownum
	}
	while (!clockIn.empty()) {
		int startTime = clockIn.top().time;
		int rownum = clockIn.top().rownum;
		clockIn.pop();
		int endTime = startTime + mDuration - 1;
		clockOut.push({ employee[rownum].end,rownum });
		while (!clockOut.empty() && clockOut.top().time < endTime)
			clockOut.pop();
		if (clockOut.size() >= M) return startTime;
	}
	return -1;
}
#endif
