```cpp
// push할때 갯수가 같으면 rightheap에 넣고 leftheap으로 넘김 → 항상 leftheap 개수가 큼.
// med를 구할때는 갯수가 같으면 rightheap에서 구하고 다르면 leftheap에서 구함.
#include <vector>
#include <queue>
using namespace std;

#define MAX_PLAYERS 39'990
#define MAX_LEAGUES 10

struct Player
{
	int ID, ability;
	bool operator<(const Player& player) const {
		return (ability < player.ability) ||
			(ability == player.ability && ID > player.ID);
	}
	bool operator>(const Player& player) const {
		return (ability > player.ability) ||
			(ability == player.ability && ID < player.ID);
	}
}players[MAX_PLAYERS];

int N, L;
struct League
{
	struct MaxPlayer
	{
		Player player;
		int idx;
		bool operator<(const MaxPlayer& max) const { return player < max.player; }
	};
	struct MinPlayer
	{
		Player player;
		int idx;
		bool operator<(const MinPlayer& min) const { return player > min.player; }
	};

	priority_queue<MaxPlayer> maxHeap, leftHeap;
	priority_queue<MinPlayer> minHeap, rightHeap;
	vector<bool> popped;
	int idx, leftSize, rightSize;
	void init() {
		while (!maxHeap.empty()) maxHeap.pop(); while (!minHeap.empty()) minHeap.pop();
		popped.clear();
		idx = 0;
		while (!leftHeap.empty()) leftHeap.pop(); while (!rightHeap.empty()) rightHeap.pop();
		leftSize = rightSize = 0;
	}
	template<typename Heap>
	void refresh(Heap& Q) {
		while (!Q.empty() && popped[Q.top().idx]) Q.pop();
	}
	 
	void push(const Player& player) {
		maxHeap.push({ player, idx });minHeap.push({ player, idx });
		popped.push_back(false);
		if (leftSize == rightSize) {
			rightHeap.push({ player,idx });
			refresh(rightHeap);
			auto top = rightHeap.top(); rightHeap.pop();
			leftHeap.push({ top.player,top.idx });
			leftSize++;
		}
		else {
			leftHeap.push({ player, idx });
			refresh(leftHeap);
			auto top = leftHeap.top(); leftHeap.pop();
			rightHeap.push({ top.player, top.idx });
			rightSize++;
		}
		idx++;
	}
	Player getMax() {
		refresh(maxHeap);
		auto top = maxHeap.top(); maxHeap.pop();
		popped[top.idx] = true;
		rightSize--;
		return top.player;
	}
	Player getMin() {
		refresh(minHeap);
		auto top = minHeap.top(); minHeap.pop();
		popped[top.idx] = true;
		leftSize--;
		return top.player;
	}
	Player getMedian() {
		Player player = {};
		if (leftSize == rightSize) {
			refresh(rightHeap);
			auto top = rightHeap.top(); rightHeap.pop();
			player = top.player;
			popped[top.idx] = true;
			rightSize--;
		}
		else {
			refresh(leftHeap);
			auto top = leftHeap.top(); leftHeap.pop();
			player = top.player;
			popped[top.idx] = true;
			leftSize--;
		}
		return player;
	}
}leagues[MAX_LEAGUES];

Player maxPlayerList[MAX_LEAGUES], minPlayerList[MAX_LEAGUES], medPlayerList[MAX_LEAGUES];

void init(int _N, int _L, int mAbility[]) {
	N = _N; L = _L;
	for (int i = 0;i < L;i++) leagues[i] = {};
	for (int i = 0; i < N;i++) leagues[i / (N / L)].push({ i,mAbility[i] });
}

int move() {
	int res = 0;
	for (int i = 0; i < L - 1; i++)
	{
		minPlayerList[i] = leagues[i].getMin();;
		maxPlayerList[i] = leagues[i + 1].getMax();
		res += minPlayerList[i].ID + maxPlayerList[i].ID;
	}
	for (int i = 0; i < L - 1; i++)
	{
		leagues[i].push(maxPlayerList[i]);
		leagues[i + 1].push(minPlayerList[i]);
	}
	return res;
}

int trade() {
	int res = 0;
	for (int i = 0; i < L - 1; i++)
	{
		medPlayerList[i] = leagues[i].getMedian();
		maxPlayerList[i] = leagues[i + 1].getMax();
		res += medPlayerList[i].ID + maxPlayerList[i].ID;
	}
	for (int i = 0; i < L - 1; i++)
	{
		leagues[i].push(maxPlayerList[i]);
		leagues[i + 1].push(medPlayerList[i]);
	}
	return res;
}
```
