```cpp
#include <queue>
#include <vector>
#include <string.h>
using namespace std;
using pii = pair<int, int>;

priority_queue<pii> down[10]; // {-point, id} 큰게 top
priority_queue<pii, vector<pii>, greater<>> up[10]; // {-point, id} 작은게 top

int carCnt, car[100003], point[100003];
vector<int> joblist[1003];

void push(int x) {
	up[car[x]].push({ -point[x],x });
	down[car[x]].push({ -point[x],x });
}

void init(int N, int M, int J, int Point[], int JobID[]) {
	carCnt = N / M;
	for (int i = 0; i < J; i++) joblist[i].clear();
	for (int i = 0; i < carCnt; i++) up[i] = {}, down[i] = {};
	for (int i = 0; i < N; i++) {
		point[i] = Point[i];
		joblist[JobID[i]].push_back(i);
		car[i] = i / M;
		push(i);
	}
}

void destroy() {}

int update(int uID, int Point) {
	point[uID] += Point;
	push(uID);
	return point[uID];
}

int updateByJob(int JobID, int Point) {
	int ret = 0;
	for (int x : joblist[JobID]) {
		point[x] += Point;
		push(x);
		ret += point[x];
	}
	return ret;
}

template<typename T>
void popNum(T& pq, int cID, int num, vector<int>& v) { // pq에서 cID 칸 탑승객
	while (num) {
		int p = -pq.top().first;
		int id = pq.top().second;
		pq.pop();
		if (point[id] != p) continue; // 최신포인트인지
		if (car[id] != cID) continue; // 최신칸인지
		if (v.size() && v.back() == id) continue; // 중복인지
		v.push_back(id);
		num--;
	}
}

int move(int num) {
	vector<int> a[10]; // a[cID] = {cID 칸으로 옮겨지는 탑승객 uID 리스트}
	int ret = 0;
	for (int i = 1; i < carCnt; i++) { // 이동하는 탑승객 a에 저장, pq에서 제거
		popNum(down[i - 1], i - 1, num, a[i]);
		popNum(up[i], i, num, a[i - 1]);
	}
	for (int i = 0; i < carCnt; i++) {
		for (int x : a[i]) {
			car[x] = i;
			push(x);
			ret += point[x];
		}
	}
	return ret;
}
```
