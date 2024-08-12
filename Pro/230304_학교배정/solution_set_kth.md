```cpp
#include<set>
#include<unordered_map>
#include<algorithm>
#include<math.h>
#include<algorithm>
using namespace std;
using pii = pair<int, int>;

int C, N, M;

unordered_map<int, int> idmap; // Student, sid

struct Student {
	int orgID; 
	int pid; // 배정된 학교 번호
	pii place[13]; // (학교와의 거리, 학교번호) 정렬해서 관리
}S[7503];

struct Comp {
	bool operator()(const int l, const int r)const {
		int distL = S[l].place[N - 1].first;
		int distR = S[r].place[N - 1].first;
		if (distL != distR) return distL > distR;
		return S[l].orgID < S[r].orgID;
	}
};

int pX[10], pY[10]; // 학교 위치
set<int, Comp>pStud[10]; // (우선순위순) 학교에 배정된 학생번호: sid
set<int, Comp>pWait[10]; // (우선순위순) 학교에 자리가 나면 재배정될 수 있는 학생번호: sid

void init(int C, int N, int mX[], int mY[]) {
	::C = C, ::N = N, M = 0;
	for (int i = 0; i < N; i++){
		pX[i] = mX[i], pY[i] = mY[i];
		pStud[i].clear();
		pWait[i].clear();
	}
	idmap.clear();
}

void addAssign(int sid) {
	for (int i = 0; i < N; i++){
		int pid = S[sid].place[i].second;
		
		if (pStud[pid].size() < C) { // 바로 수용 가능한 경우
			pStud[pid].insert(sid);
			S[sid].pid = pid;
			return;
		}

		// 수용인원 꽉 찼는데 sid의 우선순위가 최하위 학생보다는 높은 경우
		else if (Comp{}(sid, *pStud[pid].rbegin())) {
			pStud[pid].insert(sid);
			S[sid].pid = pid;
			sid = *pStud[pid].rbegin(); // sid = 최하위 학생 설정
			pStud[pid].erase(--pStud[pid].end()); // 배정상태에서 삭제
			pWait[pid].insert(sid); // 대기열에 등록하고
			addAssign(sid); // 새롭게 재배치
			return;
		}

		// 수용인원 꽉 찼는데 sid 우선순위가 최하위학생보다도 낮은 경우
		else // 다음 학교에 배정시도
			pWait[pid].insert(sid); // sid를 pid에 배치 못했으므로 대기열에 등록
	}
}

int add(int mStudent, int mX, int mY) {
	int sid = idmap[mStudent] = ++M;
	S[sid].orgID = mStudent;

	for (int i = 0; i < N; i++)
		S[sid].place[i] = { abs(mX - pX[i]) + abs(mY - pY[i]),i };
	sort(S[sid].place, S[sid].place + N);
	addAssign(sid);
	return S[sid].pid;
}

void removeAssign(int pid) {
	while (pWait[pid].size()) {
		// 최우선순위 학생을 pid에 배정
		int sid = *pWait[pid].begin();
		pStud[pid].insert(sid);

		// pid까지의 대기열에서 sid 삭제
		for (int i = N - 1; i >= 0; i--) {
			int p = S[sid].place[i].second;
			pWait[p].erase(sid);
			if (p == pid) break;
		}

		// sid가 배정되었던 기존 학교로 pid 설정후 배정 삭제
		swap(pid, S[sid].pid);
		pStud[pid].erase(sid);
	}
}

int remove(int mStudent) {
	int sid = idmap[mStudent];
	int pid = S[sid].pid;

	pStud[pid].erase(sid);
	for (int i = 0; i < N; i++)
		pWait[i].erase(sid);
	removeAssign(pid);
	return pid;
}

int status(int mSchool) {
	return pStud[mSchool].size();
}
```
