```cpp
#if 1 // 1500 ms
#include <vector>
#include <set>
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
};

int playerCnt, leagueCnt, leagueSize;

struct League
{
	set<Player> playerList;
	void clear() {	playerList.clear();	}
	void push(const Player& player) { playerList.insert(player); }
	Player getMax() {
		auto iter = --playerList.end();
		auto player = *iter;
		playerList.erase(iter);
		return player;
	}
	Player getMin() {
		auto iter = playerList.begin();
		auto player = *iter;
		playerList.erase(iter);
		return player;
	}
	Player getMedian() {
		auto iter = playerList.begin();
		advance(iter, leagueSize / 2);
		auto player = *iter;
		playerList.erase(iter);
		return player;
	}
};
League leagues[MAX_LEAGUES];

void init(int N, int L, int mAbility[]) {
	playerCnt = N;
	leagueCnt = L;
	leagueSize = N / L;
	for (int i = 0; i < L; i++) leagues[i].clear();
	for (int i = 0; i < N; i++)	leagues[i / leagueSize].push({ i, mAbility[i] });
}

int move() {
	vector<Player> maxPlayerList;
	vector<Player> minPlayerList;

	int res = 0;
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		auto minPlayer = leagues[i].getMin();
		auto maxPlayer = leagues[i + 1].getMax();

		res += minPlayer.ID + maxPlayer.ID;
		minPlayerList.push_back(minPlayer);
		maxPlayerList.push_back(maxPlayer);
	}
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		leagues[i].push(maxPlayerList[i]);
		leagues[i + 1].push(minPlayerList[i]);
	}
	return res;
}

int trade() {
	vector<Player> maxPlayerList;
	vector<Player> medPlayerList;

	int res = 0;
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		auto medPlayer = leagues[i].getMedian();
		auto maxPlayer = leagues[i + 1].getMax();

		res += medPlayer.ID + maxPlayer.ID;
		medPlayerList.push_back(medPlayer);
		maxPlayerList.push_back(maxPlayer);
	}
	for (int i = 0; i < leagueCnt - 1; i++)
	{
		leagues[i].push(maxPlayerList[i]);
		leagues[i + 1].push(medPlayerList[i]);
	}
	return res;
}
#endif
```
