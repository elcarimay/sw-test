```cpp
#include <vector>
#include <queue>
using namespace std;

#define PARTITION_SIZE 100
#define MAX_TOWER 50000
#define INF 987654321

struct Tower
{
	int r, c, color;
};

int dr[] = { -1, 0, 1, 0 }, dc[] = { 0,1,0,-1 };
Tower towers[MAX_TOWER];
int totalTowerCount, towerMap[5001][5001], mapSize;
int partitionDistMap[50000 / PARTITION_SIZE + 1][50000 / PARTITION_SIZE + 1];
queue<Tower> Q;
vector<int> tGrp[5000 / PARTITION_SIZE + 1][5000 / PARTITION_SIZE + 1][6];

void init(int N) {
	totalTowerCount = 0;
	mapSize = N;
	for (int i = 1; i <= mapSize; i++)
		for (int j = 1; j <= mapSize; j++)
			towerMap[i][j] = -1;
	for (int i = 0; i <= mapSize / PARTITION_SIZE; i++)
		for (int j = 0; j <= mapSize / PARTITION_SIZE; j++)
			for (int k = 0; k < 6; k++)
				tGrp[i][j][k].clear();
}

void buildTower(int mRow, int mCol, int mColor) {
	towers[totalTowerCount] = { mRow, mCol, mColor };
	towerMap[mRow][mCol] = totalTowerCount;
	tGrp[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][0].push_back(totalTowerCount);
	tGrp[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][mColor].push_back(totalTowerCount);
	totalTowerCount++;
}

void removeTower(int mRow, int mCol) {
	if (towerMap[mRow][mCol] == -1) return;
	int towerId = towerMap[mRow][mCol];
	int color = towers[towerId].color;
	towerMap[mRow][mCol] = -1;
	int r = mRow / PARTITION_SIZE, c = mCol / PARTITION_SIZE;
	tGrp[r][c][0].erase(remove(tGrp[r][c][0].begin(), tGrp[r][c][0].end(), towerId), tGrp[r][c][0].end());
	tGrp[r][c][color].erase(remove(tGrp[r][c][color].begin(), tGrp[r][c][color].end(), towerId), tGrp[r][c][color].end());
}

int countInnerTower(vector<int> towerIdList, Tower start, Tower end) {
	int towerCount = 0;
	for (int id : towerIdList) {
		if (towers[id].r < start.r || towers[id].c < start.c || towers[id].r > end.r || towers[id].c > end.c)
			continue;
		towerCount++;
	}
	return towerCount;
}

int countTower(int mRow, int mCol, int mColor, int mDis) {
	Tower start = { max(mRow - mDis, 1), max(mCol - mDis, 1) };
	Tower end = { min(mRow + mDis, mapSize), min(mCol + mDis, mapSize) };
	Tower startPartition = { start.r / PARTITION_SIZE, start.c / PARTITION_SIZE };
	Tower endPartition = { end.r / PARTITION_SIZE, end.c / PARTITION_SIZE };
	int towerCount = 0;

	for (int i = startPartition.r; i <= endPartition.r; i++)
		for (int j = startPartition.c; j <= endPartition.c; j++) {
			if (i == startPartition.r || i == endPartition.r || j == startPartition.c || j == endPartition.c)
				towerCount += countInnerTower(tGrp[i][j][mColor], start, end);
			else
				towerCount += tGrp[i][j][mColor].size();
		}
	return towerCount;
}

int getTowerDistance(vector<int> towerIdList, int mRow, int mCol) {
	int minTowerDistance = INF;

	for (int id : towerIdList) {
		int towerDist = abs(mRow - towers[id].r) + abs(mCol - towers[id].c);
		if (towerDist < minTowerDistance) minTowerDistance = towerDist;
	}
	return minTowerDistance;
}

int getClosest(int mRow, int mCol, int mColor) {
	for (int i = 0; i <= mapSize / PARTITION_SIZE; i++)
		for (int j = 0; j <= mapSize / PARTITION_SIZE; j++)
			partitionDistMap[i][j] = INF;
	while (!Q.empty()) Q.pop();
	Q.push({ mRow / PARTITION_SIZE, mCol / PARTITION_SIZE });
	partitionDistMap[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE] = 0;
	int minTowerDistance = INF;
	int maxPartition = mapSize / PARTITION_SIZE;
	int minPartitionDist = -1;

	while (!Q.empty()) {
		auto cur = Q.front(); Q.pop();
		int towerDist = getTowerDistance(tGrp[cur.r][cur.c][mColor], mRow, mCol);
		int partitionDist = partitionDistMap[cur.r][cur.c];

		if (towerDist < minTowerDistance) {
			minTowerDistance = towerDist;
			minPartitionDist = partitionDist;
		}

		if (minTowerDistance != INF && partitionDist - minPartitionDist > 2) break;

		for (int i = 0; i < 4; i++) {
			Tower nx = { cur.r + dr[i], cur.c + dc[i] };
			if (nx.r < 0 || nx.c < 0 || nx.r > maxPartition || nx.c > maxPartition) continue;
			if (partitionDistMap[nx.r][nx.c] <= partitionDist + 1) continue;
			partitionDistMap[nx.r][nx.c] = partitionDist + 1;
			Q.push(nx);
		}
	}
	return minTowerDistance == INF ? -1 : minTowerDistance;
}
```
