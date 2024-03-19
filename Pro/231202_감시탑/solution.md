```cpp
#include <vector>
#include <cstdio>
#include <queue>
#include <iostream>
using namespace std;

#define PARTITION_SIZE 100
#define MAX_TOWER 50000
#define INF 987654321

struct Tower
{
	int r, c, color;
};

struct Coordinate
{
	int r, c;
};

int dr[] = { -1, 0, 1, 0 }, dc[] = { 0,1,0,-1 };
Tower towers[MAX_TOWER];

int totalTowerCount, towerMap[5001][5001], mapSize;
int partitionDistMap[50000 / PARTITION_SIZE + 1][50000 / PARTITION_SIZE + 1];
queue<Coordinate> Q;
vector<int> towerGroup[5000 / PARTITION_SIZE + 1][5000 / PARTITION_SIZE + 1][6];
	
void init(int N) {
	totalTowerCount = 0;
	mapSize = N;
	for (int i = 1; i <= mapSize; i++)
		for (int j = 1; j <= mapSize; j++)
			towerMap[i][j] = -1;
	for (int i = 0; i <= mapSize / PARTITION_SIZE; i++)
		for (int j = 0; j <= mapSize / PARTITION_SIZE; j++)
			for (int k = 0; k < 6; k++)
				towerGroup[i][j][k].clear();
}
	
void buildTower(int mRow, int mCol, int mColor) {
	towers[totalTowerCount] = { mRow, mCol, mColor };
	towerMap[mRow][mCol] = totalTowerCount;
	towerGroup[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][0].push_back(totalTowerCount);
	towerGroup[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][mColor].push_back(totalTowerCount);
	totalTowerCount++;
}
	
void removeTower(int mRow, int mCol) {
	if (towerMap[mRow][mCol] == -1) return;
	int towerId = towerMap[mRow][mCol];
	int color = towers[towerId].color;
	towerMap[mRow][mCol] = -1;
	vector<int>& temp = towerGroup[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][0];
	temp.erase(remove(temp.begin(), temp.end(),towerId), temp.end());
	vector<int>& temp1 = towerGroup[mRow / PARTITION_SIZE][mCol / PARTITION_SIZE][color];
	temp1.erase(remove(temp1.begin(), temp1.end(), towerId), temp1.end());
}

int countInnerTower(vector<int> towerIdList, Coordinate start, Coordinate end) {
	int towerCount = 0;
	for (int id : towerIdList) {
		if (towers[id].r < start.r || towers[id].c < start.c || towers[id].r > end.r || towers[id].c > end.c)
			continue;
		towerCount++;
	}
	return towerCount;
}

int countTower(int mRow, int mCol, int mColor, int mDis) {
	Coordinate start = { max(mRow - mDis, 1), max(mCol - mDis, 1) };
	Coordinate end = { min(mRow + mDis, mapSize), min(mCol + mDis, mapSize) };
	Coordinate startPartition = { start.r / PARTITION_SIZE, start.c / PARTITION_SIZE };
	Coordinate endPartition = { end.r / PARTITION_SIZE, end.c / PARTITION_SIZE };
	int towerCount = 0;

	for (int i = startPartition.r; i <= endPartition.r; i++)
		for (int j = startPartition.c; j <= endPartition.c; j++) {
			if (i == startPartition.r || i == endPartition.r || j == startPartition.c || j == endPartition.c)
				towerCount += countInnerTower(towerGroup[i][j][mColor], start, end);
			else
				towerCount += towerGroup[i][j][mColor].size();
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
		Coordinate partition = Q.front(); Q.pop();
		int towerDist = getTowerDistance(towerGroup[partition.r][partition.c][mColor], mRow, mCol);
		int partitionDist = partitionDistMap[partition.r][partition.c];

		if (towerDist < minTowerDistance) {
			minTowerDistance = towerDist;
			minPartitionDist = partitionDist;
		}

		if (minTowerDistance != INF && partitionDist - minPartitionDist > 2) break;

		for (int i = 0; i < 4; i++) {
			Coordinate nextPartition = { partition.r + dr[i], partition.c + dc[i] };
			if (nextPartition.r < 0 || nextPartition.c < 0 || nextPartition.r > maxPartition || nextPartition.c > maxPartition) continue;
			if (partitionDistMap[nextPartition.r][nextPartition.c] <= partitionDist + 1) continue;
			Q.push(nextPartition);
			partitionDistMap[nextPartition.r][nextPartition.c] = partitionDist + 1;
		}
	}
	return minTowerDistance == INF ? -1 : minTowerDistance;
}
```
