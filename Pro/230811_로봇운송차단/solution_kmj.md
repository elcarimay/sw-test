```cpp
#if 0
#include <queue>
#include <unordered_map>
#define MAX_N 20
#define INSTALL 1
#define UNINSTALL -1

using namespace std;

struct Candidate {
	int y, x, dir, len;
	Candidate() {}
	Candidate(int Y, int X, int D, int L) : y(Y), x(X), dir(D), len(L) {}
};

// 약속한 방향 → 0: 북쪽, 1 : 동쪽, 2 : 남쪽, 3 : 서쪽
unordered_map<int, vector<Candidate>> candidateMap;
Candidate candidateList[5000];
queue<pair<int, int>> cQueue;
bool isblocked[MAX_N][MAX_N];
int mapSize;
int map[MAX_N][MAX_N];
int dy[] = { 0,1,0,-1 }; // 우 하 좌 상
int dx[] = { 1,0,-1,0 };

int getCandidateKey(int len, char structure[5], int dir) {
	int minHeight = 0x7fffffff;
	int key = 0;

	for (int i = 0; i < len; i++) {
		if (structure[i] < minHeight)
			minHeight = structure[i];
	}

	for (int i = 0; i < len; i++) {
		key = key * 10 + structure[i] - minHeight;
	}

	return key * 100 + len * 10 + dir;
}

int getCandidateKey(int y, int x, int len, int d) {
	char structure[5];

	for (int i = 0; i < len; i++) {
		structure[i] = 5 - map[y][x];
		y += dy[d];
		x += dx[d];
	}

	return getCandidateKey(len, structure, d);
}

void setCandidate(int y, int x) {
	for (int d = 0; d < 2; d++) { // 수평, 수직
		for (int len = 2; len <= 5; len++) {
			int ey = y + dy[d] * (len - 1);
			int ex = x + dx[d] * (len - 1);
			if (ey >= mapSize || ex >= mapSize) continue;
			int key = getCandidateKey(y, x, len, d);
			candidateMap[key].emplace_back(y, x, d, len);
		}
	}
}

// 구조물 설치가능한 후보군을 리스트에 추가하고 그 수를 리턴한다.
int getCandidate(int len, char structure[5]) {
	int wp = 0;
	for (int dir = 0; dir < 2; dir++) {
		int key = getCandidateKey(len, structure, dir);
		if (candidateMap.find(key) == candidateMap.end()) continue;
		for (auto c : candidateMap[key]) candidateList[wp++] = c;
	}
	return wp;
}

void init(int N, int mMap[MAX_N][MAX_N]){
	for (auto p : candidateMap) p.second.clear();
	candidateMap.clear();
	mapSize = N;
	memcpy(map, mMap, MAX_N * MAX_N * 4);

	for (int i = 0; i < mapSize; i++) {
		for (int j = 0; j < mapSize; j++) {
			setCandidate(i, j);
		}
	}
}

void convertStructure(int mStructure[], char structure[2][5], int M) {
	for (int i = 0; i < M; i++) {
		structure[0][i] = mStructure[i];
		structure[1][i] = mStructure[M - 1 - i];
	}
}

void setStructure(int M, char mStructure[], Candidate candidate, int installFlag) {
	for (int i = 0; i < M; i++) {
		map[candidate.y][candidate.x] += mStructure[i] * installFlag;
		candidate.y += dy[candidate.dir];
		candidate.x += dx[candidate.dir];
	}
}

void queuePush(int y, int x) {
	cQueue.emplace(y, x);
	isblocked[y][x] = false;
}

int getBlockedRobots(int dir) {
	int BlockedRobotCount = mapSize * mapSize;
	BlockedRobotCount -= mapSize; // 도착지점 제거
	memset(isblocked, 1, MAX_N * MAX_N); //400 byte 영역을 1로 초기화

	// dir에 따라서 시작위치를 우큐에 넣는 작업
	// 0: 북쪽, 1: 동쪽, 2: 남쪽, 3: 서쪽
	for (int i = 0; i < mapSize; i++) {
		if (dir == 0)
			queuePush(0, i);
		else if (dir == 1)
			queuePush(i, mapSize - 1);
		else if (dir == 2)
			queuePush(mapSize - 1, i);
		else
			queuePush(i, 0);
	}

	while (cQueue.size() > 0) {
		pair<int, int > p = cQueue.front();
		cQueue.pop();

		for (int i = 0; i < 4; i++) {
			int ny = p.first + dy[i];
			int nx = p.second + dx[i];

			if (ny < 0 || nx < 0 || ny >= mapSize || nx >= mapSize) continue;
			if (!isblocked[ny][nx] || map[p.first][p.second] > map[ny][nx]) continue;

			queuePush(ny, nx);
			BlockedRobotCount -= 1;
		}
	}
	return BlockedRobotCount;
}

int numberOfCandidate(int M, int mStructure[]){
	char structure[2][5];
	convertStructure(mStructure, structure, M);

	if (strncmp(structure[0], structure[1], M) == 0)
		return getCandidate(M, structure[0]);
	else
		return getCandidate(M, structure[0]) + getCandidate(M, structure[1]);
}

int maxBlockedRobots(int M, int mStructure[], int mDir){
	int maxNumOfBlockedRobots = 0;
	char structure[2][5];
	convertStructure(mStructure, structure, M);
	int loop = strncmp(structure[0], structure[1], M) == 0 ? 1 : 2;

	for (int i = 0; i < loop; i++) {
		int candidateCount = getCandidate(M, structure[i]);

		for (int j = 0; j < candidateCount; j++) {
			setStructure(M, structure[i], candidateList[j], INSTALL); // 구조물 설치
			int numOfBlockedRobots = getBlockedRobots(mDir);
			maxNumOfBlockedRobots = max(maxNumOfBlockedRobots, numOfBlockedRobots);
			setStructure(M, structure[i], candidateList[j], UNINSTALL); // 구조물 해체
		}
	}
	return maxNumOfBlockedRobots;
}
#endif // 1

```
