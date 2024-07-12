```cpp
#if 1
#define	S_SIZE	200001
#define	INF		0x7FFFFFFF

int min(int a, int b) { return a < b ? a : b; }
int max(int a, int b) { return a < b ? b : a; }

int streamer[S_SIZE];
struct Tree { int sum, max, min; }tree[4 * S_SIZE];

struct SegmentTree
{
	void init(int start, int end, int node) {
		if (start == end) {
			tree[node] = { streamer[start], streamer[start], streamer[start] };
			return;
		}
		int mid = (start + end) / 2;
		init(start, mid, node * 2);
		init(mid + 1, end, node * 2 + 1);
		tree[node].sum = tree[node * 2].sum + tree[node * 2 + 1].sum;
		tree[node].max = max(tree[node * 2].max, tree[node * 2 + 1].max);
		tree[node].min = min(tree[node * 2].min, tree[node * 2 + 1].min);
		return;
	}

	// index: update하고자 하는 번호
	// dif: update하고자하는 값
	void update(int start, int end, int node, int index, int dif) {
		if (index < start || index > end) return;
		if (start == end) {
			tree[node] = { tree[node].sum + dif, tree[node].max + dif, tree[node].min + dif };
			return;
		}
		int mid = (start + end) / 2;
		update(start, mid, node * 2, index, dif);
		update(mid + 1, end, node * 2 + 1, index, dif);
		tree[node].sum = tree[node * 2].sum + tree[node * 2 + 1].sum;
		tree[node].max = max(tree[node * 2].max, tree[node * 2 + 1].max);
		tree[node].min = min(tree[node * 2].min, tree[node * 2 + 1].min);
		return;
	}

	// start/end: 트리의 시작과 끝 노드
	// node: 세그먼트 트리 시작 node index
	// left, right: 구하고자하는 구간의 시작/끝
	int sum_query(int start, int end, int node, int left, int right) {
		if (end < left || right < start) return 0;
		if (left <= start && end <= right) return tree[node].sum;
		int mid = (start + end) / 2;
		return sum_query(start, mid, node * 2, left, right) + sum_query(mid + 1, end, node * 2 + 1, left, right);
	}

	int min_query(int start, int end, int node, int left, int right) {
		if (end < left || right < start) return INF; // 찾아야하는 구간과 노드구간이 겹치지 않을 때
		if (left <= start && end <= right) return tree[node].min; // 찾아야하는 구간내에 노드구간이 포함될 때
		int mid = (start + end) / 2;
		return min(min_query(start, mid, 2 * node, left, right), min_query(mid + 1, end, 2 * node + 1, left, right));
	}

	int max_query(int start, int end, int node, int left, int right) {
		if (end < left || right < start) return 0;
		if (left <= start && end <= right) return tree[node].max;
		int mid = (start + end) / 2;
		return max(max_query(start, mid, 2 * node, left, right), max_query(mid + 1, end, 2 * node + 1, left, right));
	}
}segmentTree;

int N;

void init(int _N, int mSubscriber[]) {
	N = _N;
	for (int i = 1; i <= N; i++) streamer[i] = mSubscriber[i - 1];
	segmentTree.init(1, N, 1);
	return;
}

int subscribe(int mId, int mNum) {
	streamer[mId] += mNum;
	segmentTree.update(1, N, 1, mId, mNum);
	return streamer[mId];
}

int unsubscribe(int mId, int mNum) {
	streamer[mId] -= mNum;
	segmentTree.update(1, N, 1, mId, -mNum);
	return streamer[mId];
}

int count(int sId, int eId) {
	return segmentTree.sum_query(1, N, 1, sId, eId);
}

int calculate(int sId, int eId) {
	return segmentTree.max_query(1, N, 1, sId, eId) - segmentTree.min_query(1, N, 1, sId, eId);
}
#endif

#if 0
#define	MAX_N	200001
#define	INF		0x7fffffff

inline int min(int a, int b) { return (a < b) ? a : b; }
inline int max(int a, int b) { return (a < b) ? b : a; }

int arr[MAX_N];

struct Tree {
	int sum, max, min;
};

Tree merge(const Tree& a, const Tree& b) {
	return { a.sum + b.sum, max(a.max, b.max), min(a.min, b.min) };
}

struct SegmentTree {
	Tree tree[4 * MAX_N];

	Tree init(int s, int e, int n) {
		if (s == e) return tree[n] = { arr[s], arr[s], arr[s] };
		int m = s + (e - s) / 2;
		auto a = init(s, m, n * 2);
		auto b = init(m + 1, e, n * 2 + 1);
		return tree[n] = merge(a, b);
	}

	Tree update(int s, int e, int n, int idx, int diff) {
		if (idx < s || idx > e) return tree[n];
		if (s == e) return tree[n] = { tree[n].sum + diff, tree[n].max + diff, tree[n].min + diff };
		int m = s + (e - s) / 2;
		auto a = update(s, m, n * 2, idx, diff);
		auto b = update(m + 1, e, n * 2 + 1, idx, diff);
		return tree[n] = merge(a, b);
	}

	Tree query(int s, int e, int n, int left, int right) {
		if (right < s || left > e) return { 0, -INF, INF };
		if (left <= s && e <= right) return tree[n];
		int m = s + (e - s) / 2;
		auto a = query(s, m, n * 2, left, right);
		auto b = query(m + 1, e, n * 2 + 1, left, right);
		return merge(a, b);
	}
};

SegmentTree seg;
int N;

void init(int _N, int mSubscriber[]) {
	N = _N;
	for (int i = 1; i <= N; i++)
		arr[i] = mSubscriber[i - 1];
	seg.init(1, N, 1);
}

int subscribe(int mId, int mNum) {
	arr[mId] += mNum;
	seg.update(1, N, 1, mId, mNum);
	return arr[mId];
}

int unsubscribe(int mId, int mNum) {
	arr[mId] -= mNum;
	seg.update(1, N, 1, mId, -mNum);
	return arr[mId];
}

int count(int sId, int eId) {
	return seg.query(1, N, 1, sId, eId).sum;
}

int calculate(int sId, int eId) {
	auto res = seg.query(1, N, 1, sId, eId);
	return res.max - res.min;
}
#endif

/*

- N명의 온라인 스트리머와 구독자 수

- 스트리머는 1부터 N까지 ID값

- 각 스트리머의 구독자가 증가하거나 감소

* ID 범위가 주어졌을 때, 구독자의 총합을 구하거나, 구독자의 최댓값과 최솟값의 차이를 구하고자





void init(int N, int mSubscriber[])

Parameters

  N: 스트리머 수 ( 10 ≤ N ≤ 200,000 )

  (0 ≤ i ＜ N)인 모든 i에 대해,

  mSubscriber[i]: ID가 i+1인 스트리머의 구독자 수 ( 1 ≤ mSubscriber[i] ≤ 10,000 )





int subscribe(int mId, int mNum)				횟수는 15,000 이하

- ID가 mId인 스트리머의 구독자 수가 mNum만큼 증가

- mId 스트리머의 변경된 구독자 수를 반환

Parameters

  mId: 스트리머 ID ( 1 ≤ mId ≤ N )

  mNum: 구독자 증가분 ( 1 ≤ mNum ≤ 10,000 )

Returns

  mId 스트리머의 구독자 수를 반환한다.





int unsubscribe(int mId, int mNum)				횟수는 15,000 이하

- ID가 mId인 스트리머의 구독자수가 mNum만큼 감소

- mId 스트리머의 변경된 구독자 수를 반환

* 구독자의 수가 0보다 작아지는 경우는 없다.

Parameters

  mId: 스트리머 ID ( 1 ≤ mId ≤ N )

  mNum: 구독자 감소분 ( 1 ≤ mNum ≤ 10,000 )

Returns

  mId 스트리머의 구독자 수를 반환한다.





int count(int sId, int eId)					 횟수는 15,000 이하

Parameters

  sId: 구간 처음 위치의 스트리머 ID ( 1 ≤ sId < N )

  eId: 구간 끝 위치의 스트리머 ID ( sId < eId ≤ N )

Returns

  스트리머 sId에서 eId까지 구독자의 총합을 반환한다.





int calculate(int sId, int eId)					횟수는 15,000 이하

Parameters

  sId: 구간 처음 위치의 스트리머 ID ( 1 ≤ sId < N )

  eId: 구간 끝 위치의 스트리머 ID ( sId < eId ≤ N )

Returns

  스트리머 sId에서 eId까지 구독자의 최댓값과 최솟값의 차이를 반환한다.

*/
#if 0
int main() {
	int mSubscriber[] = { 50,35,100,20,45,210,30,400,750,70 };
	//int mSubscriber[] = { 7,3,2,6,5,8,1,4 };
	init(10, mSubscriber);
	printf("%d\n", count(1, 5));
	printf("%d\n", subscribe(3, 20));
	printf("%d\n", count(1, 5));
	printf("%d\n", unsubscribe(8, 100));
	printf("%d\n", count(7, 9));
	printf("%d\n", subscribe(9, 200));
	printf("%d\n", subscribe(4, 50));
	printf("%d\n", calculate(1, 10));
	return 0;
}
#endif
```
