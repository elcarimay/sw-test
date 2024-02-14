```cpp
#if 1 // 395 ms
#include <vector>
#include <queue>
using namespace std;

#define MAX_PLAYERS 39'990
#define MAX_LEAGUES 10

struct Player
{
	int ID, ability, league;
	bool operator<(const Player& player) const {
		return (ability < player.ability) ||
			(ability == player.ability && ID > player.ID);
	}
	bool operator>(const Player& player) const {
		return (ability > player.ability) ||
			(ability == player.ability && ID < player.ID);
	}
	bool operator==(const Player& player) const {
		return ability == player.ability && ID == player.ID;
	}
};

Player players[MAX_PLAYERS];
int N, L;

struct League
{
	priority_queue<Player, vector<Player>, less<Player>> maxHeap;
	priority_queue<Player, vector<Player>, greater<Player>> minHeap;
	priority_queue<Player, vector<Player>, less<Player>> leftHeap; // x < median
	priority_queue<Player, vector<Player>, greater<Player>> rightHeap; // x > median
	int league, leftSize, rightSize;

	template<typename Heap>
	Player getTop(Heap& Q) {
		// remove popped players (TC = 2)
		while (!Q.empty() && players[Q.top().ID].league != league) Q.pop();
		auto player = Q.top(); Q.pop();

		// remove duplicate players (TC = 1)
		while (!Q.empty() && player == Q.top()) Q.pop();
		return player;
	}

	void push(const Player& player) {
		players[player.ID].league = league;
		maxHeap.push(player);
		minHeap.push(player);

		if (leftSize == rightSize) {
			rightHeap.push(player);
			auto player = getTop(rightHeap);
			leftHeap.push(player);
			leftSize++;
		}
		else if(leftSize > rightSize) { // leftSize > rightSize
			leftHeap.push(player);
			auto player = getTop(leftHeap);
			rightHeap.push(player);
			rightSize++;
		}
	}
	Player getMin() {
		auto player = getTop(minHeap);
		leftSize -= 1;
		return player;
	}
	Player getMax() {
		auto player = getTop(maxHeap);
		rightSize -= 1;
		return player;
	}
	Player getMedian() {
		Player player = {};
		if (leftSize == rightSize) {
			player = getTop(rightHeap);
			rightSize--;
		}
		else if (leftSize > rightSize) {
			player = getTop(leftHeap);
			leftSize--;
		}
		return player;
	}
};
League leagues[MAX_LEAGUES];

Player maxPlayerList[MAX_LEAGUES], minPlayerList[MAX_LEAGUES], medPlayerList[MAX_LEAGUES];

void init(int _N, int _L, int mAbility[]) {
	N = _N; L = _L;
	for (int i = 0; i < L; i++) {
		leagues[i] = {};
		leagues[i].league = i;
	}
	for (int i = 0; i < N; i++) {
		players[i] = { i, mAbility[i], i / (N / L) };
		leagues[i / (N / L)].push(players[i]);
	}
}

int move() {
	int res = 0;
	for (int i = 0; i < L - 1; i++)
	{
		auto minPlayer = leagues[i].getMin();
		auto maxPlayer = leagues[i + 1].getMax();

		res += minPlayer.ID + maxPlayer.ID;
		minPlayerList[i] = minPlayer;
		maxPlayerList[i] = maxPlayer;
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
		auto medPlayer = leagues[i].getMedian();
		auto maxPlayer = leagues[i + 1].getMax();

		res += medPlayer.ID + maxPlayer.ID;
		medPlayerList[i] = medPlayer;
		maxPlayerList[i] = maxPlayer;
	}
	for (int i = 0; i < L - 1; i++)
	{
		leagues[i].push(maxPlayerList[i]);
		leagues[i + 1].push(medPlayerList[i]);
	}
	return res;
}
#endif
```
