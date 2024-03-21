```cpp
#include <vector>
#include <unordered_map>
#include <string>
using namespace std;

#define MAX_N 1'000'000

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a < b ? a : b; }
inline int ceil(int a, int b) { return (a + b - 1) / b; }

struct Node
{
	string mName;
	int mParent, mDepth, mFirstDay, mLastDay;
};

unordered_map<string, int> nodeMap;
int nodeMapCnt;
vector<Node> nodes;

int get_LCA(int x, int y) {
	if (nodes[x].mDepth < nodes[y].mDepth) swap(x, y);
	while (nodes[x].mDepth != nodes[y].mDepth) x = nodes[x].mParent;
	while (x != y) {
		x = nodes[x].mParent; y = nodes[y].mParent;
	}
	return x;
}

struct Partition
{
	int bSize, arr[MAX_N], blocks[MAX_N];
	void init() {
		bSize = sqrt(MAX_N);
		for (int i = 0; i < MAX_N; i++) arr[i] = 0;
		for (int i = 0; i < ceil(MAX_N, bSize); i++) blocks[i] = 0;
	}
	void update(int left, int right, int value) {
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left; i <= right; i++) arr[i] += value;
			return;
		}
		for (int i = left; i <= (s + 1) * bSize - 1; i++) arr[i] += value;
		for (int i = s + 1; i <= e - 1; i++) blocks[i] += value;
		for (int i = e * bSize; i <= right; i++) arr[i] += value;
	}
	int query(int idx) {
		return arr[idx] + blocks[idx / bSize];
	}
}P;

void init(char mAncestor[], int mLastday)
{
	nodeMap[string(mAncestor)] = nodeMapCnt = 0;
	nodes.push_back({ string(mAncestor), -1, 0, 0, mLastDay });
	P.init();
	P.update(0, mLastday, 1);
}

int add(char mName[], char mParent[], int mFirstday, int mLastday)
{
	nodeMapCnt++;
	nodeMap[string(mName)] = nodeMapCnt;
	int pIdx = nodeMap[string(mParent)];
	nodes.push_back({});
	nodes[nodeMapCnt] = { string(mParent), pIdx,nodes[pIdx].mDepth + 1,mFirstday,mLastday };
	P.update(mFirstday, mLastday, 1);
	return nodes[nodeMapCnt].mDepth;
}

// LCA
int distance(char mName1[], char mName2[])
{
	int x = nodeMap[string(mName1)];
	int y = nodeMap[string(mName2)];
	int lca = get_LCA(x, y);
	return nodes[x].mDepth + nodes[y].mDepth - 2 * nodes[lca].mDepth;
}

int count(int mDay)
{
	return P.query(mDay);
}
```
