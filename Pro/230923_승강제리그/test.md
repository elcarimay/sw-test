#if 1
#include <vector>
#include <queue>
using namespace std;

#define MAX_PLAYERS 39990
#define MAX_LEAGUES 10

int playerCnt, leagueCnt, leagueSize;
struct Player
{
	int pID, ability, league;
	bool operator<(const Player& player) const {
		return (ability < player.ability) ||
			(ability == player.ability && pID > player.pID);
	}
}players[MAX_PLAYERS];

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
	int league;
	priority_queue<Player, vector<Player>, CmpMax> maxHeap;
	priority_queue<Player, vector<Player>, CmpMin> minHeap;
	Player middle;
	priority_queue<Player, vector<Player>, CmpMax> leftHeap;
	priority_queue<Player, vector<Player>, CmpMin> rightHeap;

	int leftSize, rightSize, totalSize;

	void clear() {
		while (!maxHeap.empty()) maxHeap.pop();	while (!minHeap.empty()) minHeap.pop();
		while (!leftHeap.empty()) leftHeap.pop(); while (!rightHeap.empty()) rightHeap.pop();
		leftSize = rightSize = totalSize = 0;
	}

	void push(const Player& player) {
		totalSize++;
		players[player.pID].league = league;
		maxHeap.push(player); minHeap.push(player);
		if (totalSize == 1) { middle = player; return; }
		if (player < middle) {
			leftHeap.push(player); leftSize++;
		}
		else if (middle < player) {
			rightHeap.push(player); rightSize++;
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
		while (players[middle.pID].league != middle.league)
			if (leftSize < rightSize) {
				middle = rightHeap.top(); rightHeap.pop();
			}
			else {
				middle = leftHeap.top(); leftHeap.pop();
			}
		auto player = middle;
		if (leftSize < rightSize) {
			middle = rightHeap.top(); rightHeap.pop();
			rightSize--;
		}
		else if (leftSize > rightSize) {
			middle = leftHeap.top(); leftHeap.pop();
			leftSize--;
		}
		return player.pID;
	}
}leagues[MAX_LEAGUES];

void init(int N, int L, int mAbility[]) {
	playerCnt = N; leagueCnt = L;
	leagueSize = playerCnt / leagueCnt;
	for (int i = 0; i < MAX_LEAGUES; i++) leagues[i] = {};
	for (int i = 0; i < playerCnt; i++) leagues[i].clear();
	for (int i = 0; i < playerCnt; i++) {
		players[i] = { i, mAbility[i], i / leagueSize };
		leagues[i / leagueSize].league = i / leagueSize;
		leagues[i / leagueSize].push(players[i]);
	}
}

int minIDList[MAX_LEAGUES], maxIDList[MAX_LEAGUES], midIDList[MAX_LEAGUES];

int move() {
	int ret = 0;
	for (int i = 0; i < leagueCnt - 1; i++)
		ret += (minIDList[i] = leagues[i].get_minPlayerID()) +
			(maxIDList[i] = leagues[i + 1].get_maxPlayerID());
	for (int i = 0; i < leagueCnt - 1; i++) {
		leagues[i].push(players[maxIDList[i]]);
		leagues[i+1].push(players[minIDList[i]]);
	}
	return ret;
}

int trade() {
	int ret = 0; int midp, maxp;
	for (int i = 0; i < leagueCnt - 1; i++) {
		midp = leagues[i].get_midPlayerID();
		maxp = leagues[i + 1].get_maxPlayerID();
		ret += (midIDList[i] = midp) +
			(maxIDList[i] = maxp);
	}
		/*ret += (midIDList[i] = leagues[i].get_midPlayerID()) +
		(maxIDList[i] = leagues[i + 1].get_maxPlayerID());*/
	for (int i = 0; i < leagueCnt - 1; i++) {
		leagues[i].push(players[maxIDList[i]]);
		leagues[i + 1].push(players[midIDList[i]]);
	}
	return ret;
}
#endif // 1
