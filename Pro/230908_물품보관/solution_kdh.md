```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <iostream>
#include <cmath>
using namespace std;

#define MAX_N 100005
#define INF 987654321
int goods[MAX_N];
struct Pair
{
	int idx, value;
};
struct Tree
{
	Pair max;
}tree[MAX_N * 4];
struct SegmentTree
{
	Pair max_pair(Pair a, Pair b) {
		int idx, val;
		if (a.value < b.value)	idx = b.idx, val = b.value;
		else { idx = a.idx, val = a.value; }
		return { idx, val };
	}
	void init(int start, int end, int node) {
		if (start == end) {
			tree[node] = { {start, goods[start]} };
			return;
		}
		int mid = (start + end) / 2;
		init(start, mid, node * 2);
		init(mid + 1, end, node * 2 + 1);
		tree[node] = { max_pair(tree[node * 2].max, tree[node * 2 + 1].max) };
	}
	void update(int start, int end, int node, int idx, int diff) {
		if (idx < start || end < idx) return;
		if (start == end) {
			tree[node] = { {idx,tree[node].max.value + diff} };
			return;
		}
		int mid = (start + end) / 2;
		update(start, mid, node * 2, idx, diff);
		update(mid + 1, end, node * 2 + 1, idx, diff);
		tree[node] = { max_pair(tree[node * 2].max, tree[node * 2 + 1].max) };
	}

	Pair max_query(int start, int end, int node, int left, int right) {
		if (left > end || right < start) return { -1, -INF };
		if (left <= start && end <= right) return tree[node].max;
		int mid = (start + end) / 2;
		Pair max_left = max_query(start, mid, node * 2, left, right);
		Pair max_right = max_query(mid + 1, end, node * 2 + 1, left, right);
		return max_pair(max_left, max_right);
	}
}S;

int N;
void init(int N)
{
	::N = N;
	for (int i = 1; i <= N; i++) goods[i] = 0;
	for (int i = 1; i <= N * 4; i++) tree[i] = { {-1,-INF} };
	S.init(1, N, 1);
}

Pair getMax(int left, int right) {
	return S.max_query(1, N, 1, left, right);
}

int getArea() {
	int ret = 0, cen_idx;
	// 전체
	Pair cur = getMax(1, N); cen_idx = cur.idx;
	ret += cur.value;

	// 왼쪽
	Pair prev; cur.idx = cen_idx - 1;
	while (cen_idx != 1) {
		prev = getMax(1, cur.idx);
		if (prev.value == 0) break;
		ret += (cur.idx + 1 - prev.idx) * prev.value;
		cur.idx = prev.idx - 1;
		if (prev.idx == 1) break;
	}

	// 오른쪽
	cur.idx = cen_idx + 1;
	while (cen_idx != N) {
		prev = getMax(cur.idx, N);
		if (prev.value == 0) break;
		ret += (prev.idx - (cur.idx-1)) * prev.value;
		cur.idx = prev.idx + 1;
		if (prev.idx == N) break;
	}
	return ret;
}

int stock(int mLoc, int mBox)
{
	goods[mLoc] += mBox;
	S.update(1, N, 1, mLoc, mBox);
	int ret = getArea();
	return ret;
}

int ship(int mLoc, int mBox)
{
	return stock(mLoc, -mBox);
}

int getHeight(int mLoc)
{
	return goods[mLoc];
}
#endif
```
