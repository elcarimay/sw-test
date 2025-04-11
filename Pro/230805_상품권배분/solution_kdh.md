```cpp
#if 1
#include <unordered_map>
using namespace std;
#define MAXN 1003 // group 숫자
#define MAXPART 18003 // part 숫자

unordered_map<int, int> idMap;
int idCnt;

int getID(int c) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

struct Part {
	int mid, num, parent, num_child; // id, cnt;
	bool removed;
	vector<int> child;
};

vector<Part> parts;
int N;
void init(int N, int mId[], int mNum[]) {
	::N = N, idCnt = 0, idMap.clear(), parts.clear();
	for (int i = 0; i < N; i++) parts.push_back({ getID(mId[i]), mNum[i], -1 });
}

void update_parents_nums(int mid, int num) {
	int pid = parts[mid].parent;
	while (pid != -1) {
		parts[pid].num += num;
		pid = parts[pid].parent;
	}
}

int add(int mId, int mNum, int mParent) { // mParent 값은 항상 존재하는 부서의 ID만 주어진다.
	int mid = getID(mId), pid = getID(mParent);
	if (parts[pid].num_child < 3) {
		parts.push_back({ mid, mNum, pid });
		parts[pid].num_child++;
		parts[pid].child.push_back(mid);
		update_parents_nums(mid, mNum);
		return parts[pid].num;
	}else parts.push_back({ mid }), parts[mid].removed = true;
	return -1;
}

void remove_children(int mid) {
	parts[mid].removed = true;
	for (auto cid: parts[mid].child)
		if (!parts[cid].removed) remove_children(cid);
}

int remove(int mId) { // 최상위 부서의 ID가 주어지는 경우는 없다.
	int mid = getID(mId);
	auto& p = parts[mid];
	if (p.removed) return -1;
	remove_children(mid);
	update_parents_nums(mid, -p.num);
	parts[p.parent].num_child--;
	return p.num;
}

bool exceed(int mid, int K) {
	int sum = 0;
	for (int i = 0; i < N; i++) sum += min(mid, parts[i].num);
	return sum > K ? true : false;
}

int distribute(int K) {
	int ret = 0, s = 0, e;
	for (int i = 0; i < N; i++) ret = max(ret, parts[i].num); // 매우 중요
	e = ret;
	while (s <= e) {
		int mid = (s + e) / 2;
		if (exceed(mid, K)) e = mid - 1;
		else s = mid + 1, ret = mid;
	}
	return ret;
}
#endif // 1
```
