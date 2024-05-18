```cpp
#include <map>
#include <unordered_map>
#include <string>
using namespace std;

const int MAX_TIME = 1000001;
int tree[MAX_TIME * 4];

struct SegmentTree
{
	// start, end: 노드의 시작과 끝
	// node: 세그먼트 트리 시작 node index
	// left, right: 구하고자 하는 구간의 시작/끝
	
	void init() {
		for (int i = 0; i < MAX_TIME * 4; i++) tree[i] = {};
	}

	int sum(int start, int end, int node, int idx) {
		int res = 0; res += tree[node];
		// 범위밖에 있는 경우
		if (idx > end || idx < start) return 0;
		// 범위안에 있는 경우
		if (start == end) return res;
		// 두부분으로 나누어 구하기
		int mid = (start + end) / 2;
		int left_result = sum(start, mid, node * 2, idx);
		int right_result = sum(mid+1, end, node * 2+1, idx);
		return res + left_result + right_result;
	}

	void update(int start, int end, int node, int left, int right, int dif) {
		// 범위 밖에 있는 경우
		if (right < start || left > end) return;
		if (left <= start && end <= right) { tree[node] += dif; return; }
		int mid = (start + end) / 2;
		update(start, mid, node * 2, left, right, dif);
		update(mid + 1, end, node * 2 + 1, left, right, dif);
		return;
	}
}segmentTree;

struct Node
{
	string name;
	int parent, depth, firstDay, lastDay;
};

unordered_map<string, int> nodeMap;
int nodeCnt = 0;

// LCA를 위한 정보, 부모정보, depth
const int MAX = 120000 + 1; // add로 부터 생성될 수 있는 자식의 수 + 최초 조상
Node nodes[MAX];

void init(char mAncestor[], int mLastday){
	nodeMap.clear();
	nodeMap[string(mAncestor)] = nodeCnt;
	nodes[nodeCnt++] = { string(mAncestor), -1, 0, 0, mLastday };
	segmentTree.init();
	segmentTree.update(0, 1000000, 1, 0, mLastday, 1);
	return;
}

int add(char mName[], char mParent[], int mFirstday, int mLastday){
	nodeMap[string(mName)] = nodeCnt;
	int pIdx = nodeMap[string(mParent)];
	nodes[nodeCnt] = { string(mParent), pIdx, nodes[pIdx].depth + 1, mFirstday, mLastday };
	segmentTree.update(0, 1000000, 1, mFirstday, mLastday, 1);
	return nodes[nodeCnt++].depth;
}

int get_LCA(int x, int y) {
	if (nodes[x].depth < nodes[y].depth) swap(x, y);
	while (nodes[x].depth != nodes[y].depth)
		x = nodes[x].parent;
	while (x != y) {
		x = nodes[x].parent;
		y = nodes[y].parent;
	}
	return x;
}

// LCA
int distance(char mName1[], char mName2[]){
	int x = nodeMap[string(mName1)];
	int y = nodeMap[string(mName2)];
	int lca = get_LCA(x, y);
	return nodes[x].depth + nodes[y].depth - 2 * nodes[lca].depth;
}

int count(int mDay){
	return segmentTree.sum(0,1000000, 1, mDay);
}
```
