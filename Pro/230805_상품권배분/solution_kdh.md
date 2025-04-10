```cpp
#if 1
#include <unordered_map>
using namespace std;
#define MAXN 1003 // group 숫자
#define MAXPART 17003 // part 숫자

unordered_map<int, int> idMap;
int idCnt;

int getID(int c) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

struct Part {
	int mid, num, parent, num_child; // id, cnt;
	bool removed;
	int child[3];
};

vector<Part> parts;
int N;
void init(int N, int mId[], int mNum[]) {
	::N = N, idCnt = 0, idMap.clear(), parts.clear();
	for (int i = 0; i < N; i++) parts.push_back({ getID(mId[i]), mNum[i], -1, 0,false });
}

void update_parents_nums(int mid, int num) {
	int pid = parts[mid].parent;
	while (pid != -1) {
		parts[pid].num += num;
		pid = parts[pid].parent;
	}
}

int add(int mId, int mNum, int mParent) { // mParent 값은 항상 존재하는 부서의 ID만 주어진다.
	int ret = -1, mid = getID(mId), pid = getID(mParent);
	if (parts[pid].num_child < 3) {
		parts.push_back({ mid, mNum, pid });
		parts[pid].child[parts[pid].num_child++] = mid;
		update_parents_nums(mid, mNum);
		ret = parts[pid].num;
	}
	else parts.push_back({ mid }), parts[mid].removed = true;
	return ret;
}

void remove_children(int mid) {
	parts[mid].removed = true;
	for (int i = 0; i < parts[mid].num_child; i++)
		if (!parts[parts[mid].child[i]].removed) remove_children(parts[mid].child[i]);
}

int remove(int mId) { // 최상위 부서의 ID가 주어지는 경우는 없다.
	int ret = -1, mid = getID(mId);
	auto& p = parts[mid];
	if (!idMap.count(mId) || p.removed) return ret;
	remove_children(mid);
	update_parents_nums(mid, -p.num);
	parts[p.parent].num_child--;
	ret = p.num;
	return ret;
}

bool exceed(int mid, int K) {
	int sum = 0;
	for (int i = 0; i < N; i++) {
		if (parts[i].num > mid) sum += mid;
		else sum += parts[i].num;
	}
	return sum > K ? true : false;
}

int distribute(int K) {
	int ret = N, s = N, e = 800000;
	while (s <= e) {
		int mid = (s + e) / 2;
		if(exceed(mid, K)) e = mid - 1;
		else s = mid + 1, ret = mid;
	}
	return ret;
}
#endif // 1

```
