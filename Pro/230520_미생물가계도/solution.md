```cpp
#if 0 // segmentTree by kim: 521 ms
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
		if (idx == start && end == idx) return res;
		// 두부분으로 나누어 구하기
		int mid = (start + end) / 2;
		int left_result = sum(start, mid, node * 2, idx);
		int right_result = sum(mid+1, end, node * 2+1, idx);
		return res + left_result + right_result;
	}

	int update(int start, int end, int node, int left, int right, int dif) {
		// 범위 밖에 있는 경우
		if (right < start || left > end) return tree[node];
		if (left <= start && end <= right) return tree[node] = tree[node] + dif;
		int mid = (start + end) / 2;
		int left_result = update(start, mid, node * 2, left, right, dif);
		int right_result = update(mid + 1, end, node * 2 + 1, left, right, dif);
		return left_result + right_result;
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
#endif

#if 1 // partition: 451 ms(by nam)
#include <vector>
#include <string>
#include <unordered_map>
#include <cmath>
using namespace std;

#define MAX_MICROBES	12001	// init() + add()
#define MAX_DAYS		1000001 // (0 <= mDay <= 1,000,000)

struct Microbe
{
	int parent, depth;
};
Microbe microbes[MAX_MICROBES];
unordered_map<string, int> microbeMap;
int microbeCnt, num_values, bucket_size, num_buckets;

struct Partition
{
	vector<int> buckets;	// base (not 구간합)
	vector<int> values;		// 생존 기간
	int N;					// bucket size

	void init() {
		N = bucket_size;
		buckets.clear(); buckets.resize(num_buckets);
		values.clear(); values.resize(num_values);
	}
	void update(int left, int right, int value) {
		int s = left / N;
		int e = right / N;

		if (s == e) {
			for (int i = left; i <= right; i++) values[i] += value;
			return;
		}
		while (left / N == s) { values[left++] += value; }
		while (right / N == e) { values[right--] += value; }
		for (int i = s + 1; i <= e - 1; i++) buckets[i] += value;
	}
	int query(int idx) { return values[idx] + buckets[idx / N]; }
}part;

int get_microbeIndex(const char mName[]) {
	int mIdx;
	auto res = microbeMap.find(string(mName));
	if (res == microbeMap.end()) {
		mIdx = microbeCnt++;
		microbeMap.insert({ string(mName), mIdx });
	}
	else mIdx = res->second;
	return mIdx;
}

int get_LCA(int x, int y) {
	if (microbes[x].depth < microbes[y].depth) { swap(x, y); }
	while (microbes[x].depth != microbes[y].depth) {
		x = microbes[x].parent;
	}
	while (x != y) {
		x = microbes[x].parent;
	    y = microbes[y].parent;
	}
	return x;
}

void init(char mAncestor[], int mLastday) {
	for (int i = 0; i < MAX_MICROBES; i++)	microbes[i] = {};
	microbeMap.clear(); microbeCnt = 0;

	int mIdx = get_microbeIndex(mAncestor);
	microbes[mIdx].parent = -1;
	microbes[mIdx].depth = 0;

	num_values = MAX_DAYS;
	bucket_size = sqrt(num_values); // sqrt decomposition
	num_buckets = ceil((double)num_values / bucket_size);
	part.init(); part.update(0, mLastday, 1);
}

// 12,000
int add(char mName[], char mParent[], int mFirstday, int mLastday) {
	int mIdx = get_microbeIndex(mName); 
	int pIdx = get_microbeIndex(mParent);
	microbes[mIdx].parent = pIdx;
	microbes[mIdx].depth = microbes[pIdx].depth + 1;
	part.update(mFirstday, mLastday, 1);
	return microbes[mIdx].depth;
}

// 50,000
int distance(char mName1[], char mName2[]) {
	int x = get_microbeIndex(mName1);
	int y = get_microbeIndex(mName2);
	int lca = get_LCA(x, y);

	int res = microbes[x].depth + microbes[y].depth - 2 * microbes[lca].depth;
	return res;
}

// 30,000
int count(int mDay) {
	return part.query(mDay);
}
#endif
```
