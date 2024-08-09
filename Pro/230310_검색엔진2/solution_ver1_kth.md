```cpp
#if 0 // 173 ms
/*
* 접두사별 문자열 리스트업
* 많은 양의 문자열 비교가 필요하여
* hash접근과 string 문자열 비교 연산 횟수가 많다.
*/
#define _CRT_SECURE_NO_WARNINGS
#include<string>
#include<unordered_map>
#include<vector>
#include<algorithm>
#include<string.h>
#include<stdio.h>
using namespace std;

struct Result { int mOrder, mRank; };
int gcnt, gViews[8003];
unordered_map<string, int> group;
unordered_map<string, vector<string>> prefix;

void init() {
	group.clear();
	gcnt = 0;
	prefix.clear();
}

void search(char mStr[], int mCount) {
	int gid = group[mStr];
	if (gid) gViews[gid] += mCount;
	else {
		gid = group[mStr] = ++gcnt;
		gViews[gid] = mCount;
		string s;
		prefix[s].push_back(mStr);
		for (int i = 0; mStr[i]; i++) {
			s += mStr[i];
			prefix[s].push_back(mStr);
		}
	}
}

bool comp(string& l, string& r) {
	int cl = gViews[group[l]];
	int cr = gViews[group[r]];
	if (cl != cr) return cl > cr;
	return l < r;
}

Result recommend(char mStr[]){
	string s;
	for (int i = -1; i == -1 || mStr[i]; i++) {
		if (i >= 0) s += mStr[i];
		auto& pre = prefix[s];
		int c = min(5, (int)pre.size());
		partial_sort(pre.begin(), pre.begin() + c, pre.end(), comp);
		int idx = find(pre.begin(), pre.begin() + c, mStr) - pre.begin();
		if (idx < c) {
			gViews[group[mStr]]++;
			return { i + 1, idx + 1 };
		}
	}
}

int relate(char mStr1[], char mStr2[]) {
	int gid1 = group[mStr1], gid2 = group[mStr2];
	gViews[gid1] += gViews[gid2];
	for (auto& p : group) {
		if (p.second == gid2) p.second = gid1;
	}
	return gViews[gid1];
}

void rank(char mPrefix[], int mRank, char mReturnStr[]) {
	auto& pre = prefix[mPrefix];
	partial_sort(pre.begin(), pre.begin() + mRank, pre.end(), comp);
	strcpy(mReturnStr, pre[mRank - 1].c_str());
}
#endif
```
