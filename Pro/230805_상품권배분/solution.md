```cpp
#if 1
#include <vector>
#include<unordered_map>
using namespace std;

#define NUM_NODES 17000 + 1000
#define NUM_GROUPS 1000
#define DELETED 1
struct Node
{
	int mId, num, parent, state, num_children;
	vector<int> childList;
};

unordered_map<int, int> nodeMap;
vector<Node> nodes;
vector<int> groups;
int groupCnt;

int get_nodeIndex(int mId) {
	int nIdx;
	auto ptr = nodeMap.find(mId);
	if (ptr == nodeMap.end()) nodeMap[mId] = nIdx = nodeMap.size();
	else {
		nIdx = ptr->second;
		if (nodes[nIdx].state == DELETED) nIdx = -1;
	}
	return nIdx;
}

void update_parents(int nIdx, int num) {
	int pIdx = nodes[nIdx].parent;
	while (pIdx != -1) {
		nodes[pIdx].num += num;
		pIdx = nodes[pIdx].parent;
	}
}

void remove_children(int nIdx) {
	nodes[nIdx].state = DELETED;
	for (int child : nodes[nIdx].childList)
		if (nodes[child].state != DELETED)
			remove_children(child);
}

void init(int N, int mId[], int mNum[]) {
	nodeMap.clear(), nodes.clear(), nodes.resize(NUM_NODES);
	groups.clear(), groups.resize(NUM_GROUPS); groupCnt = N;
	for (int i = 0; i < N; i++) nodes[get_nodeIndex(mId[i])] = { mId[i], mNum[i], -1 };
}

int add(int mId, int mNum, int mParent) {
	int ret = -1;
	int nIdx = get_nodeIndex(mId);
	int pIdx = get_nodeIndex(mParent);
	if (nodes[pIdx].num_children < 3) {
		nodes[pIdx].num_children++;
		nodes[pIdx].childList.push_back(nIdx);
		nodes[nIdx] = { mId, mNum, pIdx };
		update_parents(nIdx, mNum);
		ret = nodes[pIdx].num;
	}
	else nodes[nIdx].state = DELETED;
	return ret;
}

int remove(int mId) {
	int ret = -1;
	int nIdx = get_nodeIndex(mId);
	if (nIdx != -1) {
		remove_children(nIdx);
		update_parents(nIdx, -nodes[nIdx].num);
		nodes[nodes[nIdx].parent].num_children--;
		ret = nodes[nIdx].num;
	}
	return ret;
}

int distribute(int K) {
	int ret = 0;
	int start = 0, end = 0;
	for (int i = 0; i < groupCnt; i++) {
		groups[i] = nodes[i].num;
		end = max(end, groups[i]);
	}
	while (start <= end) {
		int mid = (start + end) / 2;
		int sum = 0;
		for (int i = 0; i < groupCnt; i++)
			sum += min(mid, groups[i]);
		if (sum <= K) {
			ret = mid;
			start = mid + 1;
		}
		else end = mid - 1;
	}
	return ret;
}
#endif
```
