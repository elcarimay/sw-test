```cpp
#include <unordered_map>
#define MAX_SIZE	1000
#define MIN(x, y) (((x) < (y)) ? (x) : (y))

using namespace std;
unordered_map <int, pair<int, int>> constellation; // pair.first => 중앙 좌표 코드(r * 10000 + c)의 최소값, pair.second => 별자리 타일 수
int (*map)[MAX_SIZE];
int hashKeyMap[MAX_SIZE][MAX_SIZE];

int getHashKey(int piece[5][5]) {
	int temp[4] = { 0,0,0,0 };

	for (int i = 0; i < 5; i++)	{
		for (int j = 0; j < 5; j++)	{
			temp[0] = temp[0] * 2 + piece[i][j];
			temp[1] = temp[1] * 2 + piece[j][4 - i];
			temp[2] = temp[2] * 2 + piece[4 - i][4 - j];
			temp[3] = temp[3] * 2 + piece[4 - j][i];
		}
	}

	int minHashKey = temp[0];

	for (int i = 1; i < 4; i++) {
		minHashKey = MIN(minHashKey, temp[i]);
	}

	return minHashKey;
}

int copyMap(int offsetY, int offsetX, int dest[5][5]) {
	int count = 0;
	for (int i = 0; i < 5; i++) {
		for (int j = 0; j < 5; j++) {
			dest[i][j] = map[offsetY + i][offsetX + j];
			if (dest[i][j])
				count += 1;
		}
	}
	return count;
}

int addConstellation(int offsetY, int offsetX) {
	int piece[5][5];
	copyMap(offsetY, offsetX, piece);
	int hashKey = getHashKey(piece);
	int centerCoordinate = (offsetY + 2) * 10000 + offsetX + 2;

	if (constellation.find(hashKey) == constellation.end()) {
		constellation.emplace(hashKey, make_pair(centerCoordinate, 0));
	}

	constellation[hashKey].first = MIN(centerCoordinate, constellation[hashKey].first);
	constellation[hashKey].second += 1;
	return hashKey;
}

void copyHashKeyToMap(int offsetY, int offsetX, int hashKey) {
	for (int i = 0; i < 5; i++) {
		for (int j = 0; j < 5; j++) {
			hashKeyMap[offsetY + i][offsetX + j] = hashKey;
		}
	}
}

void init(int N, int mPlane[MAX_SIZE][MAX_SIZE])
{
	map = mPlane;
	constellation.clear();

	int temp[5][5];
	int startCount;

	for (int i = 0; i <= N - 5; i++) {

		startCount = copyMap(i, 0, temp);
		if (startCount == 7)
			copyHashKeyToMap(i, 0, addConstellation(i, 0));

		for (int j = 1; j <= N - 5; j++) {

			int wpColumn = (j - 1) % 5;

			for (int k = 0; k < 5; k++) {
				startCount += mPlane[i + k][j + 4] - temp[k][wpColumn];
				temp[k][wpColumn] = mPlane[i + k][j + 4];
			}

			if (startCount == 7)
				copyHashKeyToMap(i, j, addConstellation(i, j));
		}
	}
}

int getCount(int mPiece[5][5])
{
	int hashKey = getHashKey(mPiece);

	if (constellation.find(hashKey) == constellation.end())
		return 0;

	return constellation[hashKey].second;
}

int getPosition(int mRow, int mCol)
{
	return constellation[hashKeyMap[mRow][mCol]].first;
}
```
