```cpp
#if 1
// Parametric Search
// 기지국 간격에 따른 기지국 갯수가 주어진 M값을 만족하는지를 조건함수로 만듬
// 기지국 간격을 작게 잡을수록 갯수가 증가하기 때문에 갯수가 M개이상 증가시 간격을 늘인다 
#include <unordered_map>
#include <map>
using namespace std;

unordered_map<int, int> idMap;
map<int, int> loc;

int add(int mId, int mLocation) {
	if (idMap.count(mId)) loc.erase(idMap[mId]);
	idMap[mId] = mLocation;
	loc[mLocation] = mId;
	return (int)loc.size();
}

void init(int N, int mId[], int mLocation[]) {
	idMap.clear(), loc.clear();
	for (int i = 0; i < N; i++) add(mId[i], mLocation[i]);
}

int remove(int mStart, int mEnd) {
	for (map<int, int>::iterator it = loc.lower_bound(mStart); it != loc.end() && it->first <= mEnd;)
		idMap.erase(it->second), it = loc.erase(it);
	return loc.size();
}

//map순회(2가지 변수를 다 들고다님) // 170 ms
//bool isPossible(int limit, int m) {
//	int cnt = 1, cur = loc.begin()->first;
//	for (map<int, int>::iterator it = loc.begin(); it != loc.end(); it++) {
//		if (it->first - cur >= limit) {
//			cur = it->first;
//			if (++cnt >= m) return true;
//		}
//	}
//	return false;
//}
//
//int install(int M) {
//	int s = 0, mid;
//	int e = loc.rbegin()->first - loc.begin()->first;
//	while (s <= e) {
//		mid = (s + e) / 2;
//		if (isPossible(mid, M)) s = mid + 1;
//		else e = mid - 1;
//	}
//	return e;
//}

vector<int> v; // 121 ms
// 간격을 작게 잡을수록 갯수가 증가하기 때문에 갯수가 M개이상 증가시 간격을 늘인다
bool isPossible(int limit, int m) {
	int cnt = 1, cur = v[0];
	for (int i = 1; i < (int)v.size();i++) {
		if (v[i] - cur >= limit) {
			cur = v[i];
			if (++cnt >= m) return true;
		}
	}
	return false;
}

int install(int M) {
	v.clear();
	for (auto nx : loc) v.push_back(nx.first);
	int s = 0, mid;
	int e = *v.rbegin() - *v.begin();
	while (s <= e) {
		mid = (s + e) / 2;
		if (isPossible(mid, M)) s = mid + 1;
		else e = mid - 1;
	}
	return e;
}

//int a[24003], head, tail; // 129 ms
//// 간격을 작게 잡을수록 갯수가 증가하기 때문에 갯수가 M개이상 증가시 간격을 늘인다
//bool isPossible(int limit, int m) {
//	int cnt = 1, cur = a[0]; head = 1;
//	while(head < tail){
//		if (a[head++] - cur >= limit) {
//			cur = a[head - 1];
//			if (++cnt >= m) return true;
//		}
//	}
//	return false;
//}
//
//int install(int M) {
//	head = tail = 0;
//	for (auto nx : loc) a[tail++] = nx.first;
//	int s = 0, mid;
//	int e = a[tail - 1] - a[0];
//	while (s <= e) {
//		mid = (s + e) / 2;
//		if (isPossible(mid, M)) s = mid + 1;
//		else e = mid - 1;
//	}
//	return e;
//}
#endif // 1 //170 ms
```
