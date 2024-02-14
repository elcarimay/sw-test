```cpp
#if 1 // pq: 325 ms → 270 ms
#include <vector>
#include <queue>
using namespace std;

#define MAX_PLAYERS 39990
#define MAX_LEAGUES 10

int playerCnt; // N: 선수들의 수(9 ≤ N ≤ 39,990)
int leagueCnt; // L: 리그의 개수(3 ≤ L ≤ 10, 3 ≤ N / L ≤ 3,999)
int leagueSize; // league size = playerCnt / leagueCnt : N / L : 하나의 리그에 포함된 선수들의 수

struct Player
{
	int pID, ability, league;
	bool operator<(const Player& player)const {
		return (ability < player.ability) ||
			(ability == player.ability && pID > player.pID);
	}
};

Player players[MAX_PLAYERS];

struct CmpMax
{
	bool operator()(const Player& p1, const Player& p2) const {
		return (p1.ability < p2.ability) ||
			(p1.ability == p2.ability && p1.pID > p2.pID);
	}
};

struct CmpMin
{
	bool operator()(const Player& p1, const Player& p2) const {
		return (p1.ability > p2.ability) ||
			(p1.ability == p2.ability && p1.pID < p2.pID);
	}
};

struct League
{
	int league; // league index
	priority_queue<Player, vector<Player>, CmpMax> maxHeap;
	priority_queue<Player, vector<Player>, CmpMin> minHeap;
	
	Player middle;

	priority_queue<Player, vector<Player>, CmpMax> leftHeap; // player < middle(max. heap)
	priority_queue<Player, vector<Player>, CmpMin> rightHeap; // player > middle(min. heap)

	int leftSize, rightSize, totalSize;

	void clear() {
		while (!maxHeap.empty()) maxHeap.pop();
		while (!minHeap.empty()) minHeap.pop();
		while (!leftHeap.empty()) leftHeap.pop();
		while (!rightHeap.empty()) rightHeap.pop();
		leftSize = 0;
		rightSize = 0;
		totalSize = 0;
	}

	void push(const Player& player) {
		totalSize++;
		players[player.pID].league = league;
		maxHeap.push(player);
		minHeap.push(player);
		if (totalSize == 1) { middle = player; return; }
		if (player < middle) {
			leftHeap.push(player);
			leftSize++;
		}
		else if (middle < player) {
			rightHeap.push(player);
			rightSize++;
		}
		if (leftSize == rightSize || totalSize % 2 == 0) return;
		else if (leftSize < rightSize) {
			leftHeap.push(middle);
			middle = rightHeap.top(); rightHeap.pop();
			leftSize++; rightSize--;
		}
		else if (leftSize > rightSize) {
			rightHeap.push(middle);
			middle = leftHeap.top(); leftHeap.pop();
			leftSize--; rightSize++;
		}
	}
	int get_maxPlayerID() {
		rightSize--; totalSize--;
		auto player = maxHeap.top(); maxHeap.pop();
		return player.pID;
	}
	int get_minPlayerID() {
		leftSize--; totalSize--;
		auto player = minHeap.top(); minHeap.pop();
		return player.pID;
	}
	int get_midPlayerID() {
		totalSize--;
		while(players[middle.pID].league != middle.league)
			if (leftSize < rightSize) { middle = rightHeap.top(); rightHeap.pop(); }
			else { middle = leftHeap.top(); leftHeap.pop(); }
		auto player = middle;
		if (leftSize < rightSize) {
			middle = rightHeap.top(); rightHeap.pop();
			rightSize--;
		}
		else {
			middle = leftHeap.top(); leftHeap.pop();
			leftSize--;
		}
		return player.pID;
	}
};

League leagues[MAX_LEAGUES];

void init(int N, int L, int mAbility[]) {
	playerCnt = N;
	leagueCnt = L;
	leagueSize = playerCnt / leagueCnt;
	for (int i = 0; i < MAX_LEAGUES; i++) leagues[i] = {};
	for (int i = 0; i < playerCnt; i++)
	{
		players[i] = {i, mAbility[i], i / leagueSize};
		leagues[i / leagueSize].league = i / leagueSize;
		leagues[i / leagueSize].push(players[i]);
	}
}

int minIDList[MAX_LEAGUES], maxIDList[MAX_LEAGUES], midIDList[MAX_LEAGUES];

int move() {
	int ret = 0;
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		int minID = leagues[i].get_minPlayerID();
		int maxID = leagues[i + 1].get_maxPlayerID();
		ret += minID + maxID;
		minIDList[i] = minID;
		maxIDList[i] = maxID;
	}
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		leagues[i].push(players[maxIDList[i]]);
		leagues[i + 1].push(players[minIDList[i]]);
	}
	return ret;
}

int trade() {
	int ret = 0;
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		int midID = leagues[i].get_midPlayerID();
		int maxID = leagues[i + 1].get_maxPlayerID();
		ret += midID + maxID;
		midIDList[i] = midID;
		maxIDList[i] = maxID;
	}
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		leagues[i].push(players[maxIDList[i]]);
		leagues[i + 1].push(players[midIDList[i]]);
	}
	return ret;
}
#endif
```
