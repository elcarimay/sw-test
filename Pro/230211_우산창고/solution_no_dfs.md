```cpp
#if 1
#include <vector>
#include <queue>
using namespace std;

inline int min(int a, int b) { return a < b ? a : b; }

struct Data
{
	int id, dist;
	bool operator<(const Data& data)const {
		return (dist > data.dist) ||
			(dist == data.dist && id > data.id);
	}
};

struct  Node
{
	int mId, mpId, mDepth, mDist, mQuantity, mTotal;
	vector<Data> childList;
	void update_quantity(int mQuantity) {
		this->mQuantity += mQuantity;
		this->mTotal += mQuantity;
	}
};

vector<Node> nodes;

void update_parents(int mIdx, int quantity) {
	int pIdx = nodes[mIdx].mpId;
	while (pIdx != -1) {
		nodes[pIdx].mTotal += quantity;
		pIdx = nodes[pIdx].mpId;
	}
}

void init(int N, int mParent[], int mDistance[], int mQuantity[]) {
	nodes.clear(); nodes.resize(N);
	nodes[0] = { 0, mParent[0] };
	nodes[0].update_quantity(mQuantity[0]);
	for (int i = 1; i < N; i++)
	{
		nodes[i] = { i, mParent[i],nodes[mParent[i]].mDepth + 1, nodes[mParent[i]].mDist + mDistance[i] };
		nodes[i].update_quantity(mQuantity[i]);
		update_parents(i, mQuantity[i]);
		nodes[i].childList.push_back({ mParent[i], mDistance[i] });
		nodes[mParent[i]].childList.push_back({ i, mDistance[i] });
	}
}

int get_LCA(int x, int y) {
	if (nodes[x].mDepth < nodes[y].mDepth) swap(x, y);
	while (nodes[x].mDepth != nodes[y].mDepth) x = nodes[x].mpId;
	while (x != y) {
		x = nodes[x].mpId; y = nodes[y].mpId;
	}
	return x;
}

int carry(int mFrom, int mTo, int mQuantity) {
	nodes[mFrom].update_quantity(-mQuantity);
	update_parents(mFrom, -mQuantity);
	nodes[mTo].update_quantity(mQuantity);
	update_parents(mTo, mQuantity);
	int lca = get_LCA(mFrom, mTo);
	int distance = nodes[mFrom].mDist + nodes[mTo].mDist - nodes[lca].mDist * 2;
	return distance * mQuantity;
}

int gather(int mId, int mQuantity) {
	priority_queue<Data> Q;
	vector<bool> visited(nodes.size());
	int ret = 0;
	visited[mId] = true;
	Q.push({ mId, 0 });
	while (!Q.empty() && mQuantity > 0) {
		auto cur = Q.top(); Q.pop();
		if (cur.id != mId) {
			int quantity = min(mQuantity, nodes[cur.id].mQuantity);
			ret += carry(cur.id, mId, quantity);
			mQuantity -= quantity;
		}
		for (auto next : nodes[cur.id].childList)
			if (!visited[next.id]) {
				visited[next.id] = true;
				int lca = get_LCA(next.id, mId);
				int dist = nodes[next.id].mDist + nodes[mId].mDist - nodes[lca].mDist * 2;
				Q.push({ next.id, dist });
			}
	}
	return ret;
}

int sum(int mId) {
	return nodes[mId].mTotal;
}
#endif
```
