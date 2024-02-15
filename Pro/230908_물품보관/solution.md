```cpp
// 힙정렬일때 n log2 n * 20000

#define MAX_N 100000
#define MAX_L 320
#define MAX_P (100000 / MAX_L + 1)
#define LEFT 0
#define RIGHT 1

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a < b ? a : b; }
int endLoc;
int box[MAX_N];

struct Partition {
	int st, ed, topLoc;

	void addBox(int mLoc, int mBox) {
		box[mLoc] += mBox;
		topLoc = st;
		for (int i = st; i < ed; i++)
			if (box[i] > box[topLoc]) topLoc = i;
	}

	int getTopLocOnTheLeft(int from, int to) { // 구하고자하는 구간이 파티션에 걸칠때 사용
		int ost = max(st, from);
		int oed = min(ed, to);
		int ptrTopLoc = ost;

		for (int i = ost; i < oed; i++)
			if (box[i] > box[ptrTopLoc]) ptrTopLoc = i;
		return ptrTopLoc;
	}

	int getTopLocOnTheRight(int from, int to) {
		int ost = max(st, from);
		int oed = min(ed, to);
		int ptrTopLoc = ost;

		for (int i = ost; i < oed; i++)
			if (box[i] >= box[ptrTopLoc]) ptrTopLoc = i;
		return ptrTopLoc;
	}
};

Partition partition[MAX_P];

int getPartitionId(int mLoc) { return mLoc / MAX_L; }

void init(int N){
	endLoc = N + 1;
	int maxPartitionId = N / MAX_L;
	for (int i = 0; i <= N; i++) box[i] = 0;
	for (int i = 0; i <= maxPartitionId; i++)
	{
		partition[i].st = i * MAX_L;
		partition[i].ed = (i + 1) * MAX_L;
		partition[i].topLoc = partition[i].st;
	}
}

int getTopLocOnTheLeft(int st, int ed) { // 높이가 같다면 더 왼쪽에 있는 위치를 선택
	int topLoc = st;
	int sp = getPartitionId(st);
	int ep = getPartitionId(ed - 1);
	int loc;
	for (int i = sp; i <= ep; i++)
	{
		// i번째 파티션이 st, ed 구간에 온전히 속한다면 이미 구해진 topLoc를 활용하고
		// 그렇지 않다면 각 요소를 비교해서 topLoc를 구한다.
		if (st <= partition[i].st && partition[i].ed <= ed) loc = partition[i].topLoc;
		else loc = partition[i].getTopLocOnTheLeft(st, ed);
		if (box[loc] > box[topLoc]) topLoc = loc; // > 해야 왼쪽에 가장 높은위치가 검색됨.
	}
	return topLoc;
}

int getTopLocOnTheRight(int st, int ed) {
	int topLoc = st;
	int sp = getPartitionId(st);
	int ep = getPartitionId(ed - 1);
	int loc;
	for (int i = sp; i <= ep; i++)
	{
		if (st <= partition[i].st && partition[i].ed <= ed) loc = partition[i].topLoc;
		else loc = partition[i].getTopLocOnTheRight(st, ed);
		if (box[loc] >= box[topLoc]) topLoc = loc; // >= 해야 오른쪽에 가장 높은위치가 검색됨.
	}
	return topLoc;
}

int putUpLeftSide(int ed) { // n < mLoc 범위를 특수 천막으로 덮고 덮힌 넓이를 리턴한다.
	if (ed == 0) return 0;
	int loc = getTopLocOnTheLeft(0, ed); // 0 ~ ed 까지의 최대값을 얻음.
	return box[loc]*(ed - loc) + putUpLeftSide(loc); // 최대값을 넣고 그 최대값까지의 최대값을 리턴함.
}

int putUpRightSide(int st) { // n >= mLoc 범위를 특수 천막으로 덮고 덮힌 넓이를 리턴한다.
	if (st == endLoc) return 0;
	int loc = getTopLocOnTheRight(st, endLoc);
	return box[loc] * (loc + 1 - st) + putUpRightSide(loc + 1);
}

int stock(int mLoc, int mBox){
	
	partition[getPartitionId(mLoc)].addBox(mLoc, mBox);
	int loc = getTopLocOnTheLeft(0, endLoc); // 1. 가장 높은 위치를 찾는다.
	
	// 2. k의 왼쪽 방향으로 천막을 덮는다. 덮으면서 덮힌 넓이도 구한다.
	// 3. k의 오른쪽 방향으로 천막을 덮는다. 덮으면서 덮힌 넓이도 구한다.
	// 4. 넓이를 다 더해서 리턴한다.
	return putUpLeftSide(loc) + box[loc] + putUpRightSide(loc + 1);
}

int ship(int mLoc, int mBox){
	return stock(mLoc, -mBox);
}

int getHeight(int mLoc){
	return box[mLoc];
}
```
