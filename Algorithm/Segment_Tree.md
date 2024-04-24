```cpp
#if 1
#define	S_SIZE	200001
#define	INF	987654321

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
		if (left > end || right < start) return 0;
		if (left <= start && end <= right) return tree[node].sum;
		int mid = (start + end) / 2;
		int sum_left = sum_query(start, mid, node * 2, left, right);
		int sum_right = sum_query(mid + 1, end, node * 2 + 1, left, right);
		return sum_left + sum_right;
	}

	int min_query(int start, int end, int node, int left, int right) {
		if (end < left || right < start) return INF; // 찾아야하는 구간과 노드구간이 겹치지 않을 때
		if (left <= start && end <= right) return tree[node].min; // 찾아야하는 구간내에 노드구간이 포함될 때
		int mid = (start + end) / 2;
		int min_left = min_query(start, mid, 2 * node, left, right);
		int min_right = min_query(mid + 1, end, 2 * node + 1, left, right);
		return min(min_left, min_right);
	}

	int max_query(int start, int end, int node, int left, int right) {
		if (end < left || right < start) return -INF;
		if (left <= start && end <= right) return tree[node].max;
		int mid = (start + end) / 2;
		int max_left = max_query(start, mid, 2 * node, left, right);
		int max_right = max_query(mid + 1, end, 2 * node + 1, left, right);
		return max(max_left, max_right);
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
```
