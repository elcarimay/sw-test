# C++ 관련 Sites

## 그냥 개인 site
https://blog.naver.com/miracle_start/223022004124

## 설명이 잘되어 있는 site
https://ansohxxn.github.io/stl/map/

https://modoocode.com/224
(C 및 C++ 강의가 잘 정리되어 있음.)  
  
알고리즘 설명  
Union-Find, Segment Tree  
https://yoongrammer.tistory.com/102  
https://cafe.naver.com/pssasa/1543?art=ZXh0ZXJuYWwtc2VydmljZS1uYXZlci1zZWFyY2gtY2FmZS1wcg.eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjYWZlVHlwZSI6IkNBRkVfVVJMIiwiY2FmZVVybCI6InBzc2FzYSIsImFydGljbGVJZCI6MTU0MywiaXNzdWVkQXQiOjE3MTcyMjg2MzI4MzZ9.DoiSyloi4rN6aS2OWlXk3gFItnUMjR8VqdYfjiHw-CI  


#include <vector>
#include <iostream>
#include <string.h>
#include <unordered_map>
#include <algorithm>
#include <queue>
using namespace std;

struct Data {
	int id, time;
	bool operator<(const Data& r) const {
		return r.time > time;
	}
};

int N;
unordered_map<int, int> mMap;
priority_queue<Data> pq;

void init(int N, int mId[], int mTime[]) {
	::N = N; mMap.clear();
	while (!pq.empty()) pq.pop();
	for (int i = 0; i < N; i++) {
		mMap[mId[i]] = mTime[i];
		pq.push({ mId[i],mTime[i] });
	}
}

int add(int mId, int mTime) {
	pq.push({ mId,mTime });
	auto id = mMap.find(mId);
	if (id == mMap.end()) {
		mMap[mId] = mTime;
		pq.push({ mId,mTime });
	}
	else mMap[mId] = mTime;
	return mMap.size();
}

int remove(int K) {
	while (K--) {
		auto t = pq.top(); pq.pop();
		while (t.time != mMap[t.id]) {
			t = pq.top(); pq.pop();
		}
		auto it = mMap.find(t.id);
		mMap.erase(it);
	}
	return pq.top().id;
}

int ret;
bool check(int m, int M) {
	int num = 0;
	for (auto nx : mMap) {
		num += int(m / nx.second);
	}
	if (num == M) ret = m;
	return num < M;
}

int produce(int M) {
	int s = 0, e = 100 * 3000;
	int m;
	ret = 0;
	while (s <= e) {
		m = (s + e) / 2;
		if (check(m, M)) s = m + 1;
		else e = m - 1;
	}
	return ret;
}
