```cpp
#if 1 // 43 ms
// Non-recursive descison-tree; the root index is 0.
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

struct Node {
	int x1, y1, x2, y2, rep, loss;
	int left = -1, right = -1; // index in tree vector, -1 -> none
	bool leaf = true;
};

int N, K;
vector<vector<int>> grid; // N x N, 0 = empty
unordered_map<int, tuple<int, int, int>> samples; // id -> (x, y, c)
vector<vector<vector<int>>> pref; // pref[col][i][j], 1-based
vector<Node> tree;
// x1 < x2, y1 < y2
int rectCount(int col, int x1, int y1, int x2, int y2) { // returns number of cells of colour 'col' inside the inclusive rectangle
	return pref[col][x2 + 1][y2 + 1] - pref[col][x1][y2 + 1] - pref[col][x2 + 1][y1] + pref[col][x1][y1];
}

void analyseRect(int x1, int y1, int x2, int y2, int& rep, int& loss) { // compute its majority colour and loss
	int bestCnt = -1, bestCol = 1, total = 0;
	for (int col = 1; col <= K; col++) {
		int cnt = rectCount(col, x1, y1, x2, y2);
		total += cnt;
		if (cnt > bestCnt || (cnt == bestCnt && col < bestCol)) bestCnt = cnt, bestCol = col;
	}
	rep = bestCol, loss = total - bestCnt;
}

struct SplitCandidate {
	bool exists = false; // a split that really improves the loss
	bool vertical = true; // true -> split along x (left/right)
	int pos = -1; // split coorinate(x-line or y-line)
	int lossSum = INT_MAX; // loss(left) + loss(right)
};

SplitCandidate bestSplit(int x1, int y1, int x2, int y2, int curLoss) {
	SplitCandidate best;
	// vertical splits (split between columns x...x+1)
	for (int x = x1; x < x2; x++) {
		int leftBest = -1, rightBest = -1, leftTot = 0, rightTot = 0;
		for (int col = 1; col <= K; col++) {
			int l = rectCount(col, x1, y1, x, y2);
			int r = rectCount(col, x + 1, y1, x2, y2);
			leftTot += l, rightTot += r;
			leftBest = max(leftBest, l), rightBest = max(rightBest, r);
		}
		int lossSum = (leftTot - leftBest) + (rightTot - rightBest);
		if (lossSum < curLoss) {
			if (!best.exists || lossSum < best.lossSum ||
				(lossSum == best.lossSum && (!best.vertical || x < best.pos)))
				best = { true, true, x, lossSum };
		}
	}
	// horizontal splits
	for (int y = y1; y < y2; y++) {
		int topBest = -1, botBest = -1, topTot = 0, botTot = 0;
		for (int col = 1; col <= K; col++) {
			int t = rectCount(col, x1, y1, x2, y);
			int b = rectCount(col, x1, y + 1, x2, y2);
			topTot += t, botTot += b;
			topBest = max(topBest, t), botBest = max(botBest, b);
		}
		int lossSum = (topTot - topBest) + (botTot - botBest);
		if (lossSum < curLoss) { // 자식노드 손실합이 부모노드보다
			if (!best.exists || lossSum < best.lossSum ||
				(lossSum == best.lossSum && !best.vertical && y < best.pos) ||
				(lossSum == best.lossSum && best.vertical && !best.vertical && y < best.pos))
				best = { true, false, y, lossSum };
		}
	}
	return best;
}

void buildTreeIterative() {
	struct Frame {
		int x1, y1, x2, y2, parentIdx;
		bool isLeftChild;
	};
	tree.clear();
	queue<Frame> st;
	st.push({ 0,0,N - 1, N - 1, -1, false }); // start from the whole grid;
	while (!st.empty()) {
		Frame cur = st.front(); st.pop();
		Node nd;
		nd.x1 = cur.x1; nd.y1 = cur.y1; nd.x2 = cur.x2; nd.y2 = cur.y2;
		analyseRect(nd.x1, nd.y1, nd.x2, nd.y2, nd.rep, nd.loss); // nd.rep와 nd.loss는 주소값으로 넘겨서 반환됨
		nd.leaf = true; nd.left = nd.right = -1;
		// decide whether to split
		SplitCandidate split = bestSplit(nd.x1, nd.y1, nd.x2, nd.y2, nd.loss);
		int myIdx = (int)tree.size();
		tree.push_back(nd);
		if (split.exists) {
			tree[myIdx].leaf = false;
			// children will be processed later; push them onto the stack.
			// Important: push the right child first because the stack is LIFO
			// and we want to visit the left child before the right child
			if (!split.vertical) { // horizontal split -> top / bottom
				st.push({ nd.x1, split.pos + 1, nd.x2, nd.y2, myIdx, false }); // bottom(right) child
				st.push({ nd.x1, nd.y1, nd.x2, split.pos, myIdx, true }); // top(left) child
			}
			else { // vertical split -> left / right
				st.push({ split.pos + 1, nd.y1, nd.x2, nd.y2, myIdx, false }); // right child
				st.push({ nd.x1, nd.y1, split.pos, nd.y2, myIdx, true }); // left child
			}
		}
		// after children have been processed we can fix the parent links.
		// the parent index is stored in the frame(parentIdx). when the parent is still -1 we are processing the root.
		if (cur.parentIdx != -1)
			if (cur.isLeftChild) tree[cur.parentIdx].left = myIdx;
			else tree[cur.parentIdx].right = myIdx;
	}
}

int predictIterative(int x, int y) {
	int idx = 0;
	while (!tree[idx].leaf) {
		const Node& cur = tree[idx];
		const Node& left = tree[cur.left];
		// left child is always the rectangle that contains the split line
		// (i.e. the one that was pushed first during construction)
		if (x >= left.x1 && x <= left.x2 && y >= left.y1 && y <= left.y2) idx = cur.left;
		else idx = cur.right;
	}
	return tree[idx].rep;
}

void init(int N, int K) {
	::N = N, ::K = K, samples.clear();
	grid.assign(N, vector<int>(N, 0));
}

void addSample(int mID, int mX, int mY, int mC) {
	samples[mID] = make_tuple(mX, mY, grid[mX][mY] = mC);
}

void deleteSample(int mID) {
	auto it = samples.find(mID);
	if (it == samples.end()) return;
	int x, y, c;
	tie(x, y, c) = it->second;
	grid[x][y] = 0;
	samples.erase(it);
}

int fit() {
	pref.assign(K + 1, vector<vector<int>>(N + 1, vector<int>(N + 1, 0)));
	for (int col = 1; col <= K; col++)
		for (int i = 0; i < N; i++) {
			int rowSum = 0;
			for (int j = 0; j < N; j++) {
				rowSum += (grid[i][j] == col);
				pref[col][i + 1][j + 1] = pref[col][i][j + 1] + rowSum;
				// pref[col][i][j+1]: i번째 이전 행까지 열 0~j까지의 col 개수
				// rowSum: 현재 행i에 대해서 열 0~j까지 col 개수
			}
		}
	buildTreeIterative();
	long long totalLoss = 0;
	for (const Node& nd : tree) if (nd.leaf) totalLoss += nd.loss;
	return (int)totalLoss;
}

int predict(int mX, int mY) {
	int ret = predictIterative(mX, mY);
	return ret;
}

#endif // 1 // 43 ms
```
