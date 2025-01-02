```cpp
#include<vector>
#include<unordered_map>
#include<algorithm>
using namespace std;

struct Info {
	int id1, id2;
};

const int INF = 1e9;
int L[205], n;
unordered_map<int, vector<Info>> hmap;
struct Data {
	int maxL, id[3];
};
vector<Data> v;

void init(){
	n = 0; hmap.clear();
}

void addBeam(int mLength){
	L[++n] = mLength; 
	for (int i = 1; i < n; i++)
		hmap[L[i] + mLength].push_back({ i, n });
}

void setEntry(int h) {
	v.clear();
	for (int i = 0; i <= n; i++) {
		if (L[i] == h) v.push_back({ L[i], i });
		else if (L[i] < h) {
			if (hmap.find(h - L[i]) == hmap.end()) continue;
			for (Info p : hmap[h - L[i]]) {
				int j = p.id1, k = p.id2;
				if (i == j || i == k) continue;
				int maxL = max(L[i], max(L[j], L[k]));
				v.push_back({ maxL, i, j, k });
			}
		}
	}
}

int requireSingle(int mHeight){
	setEntry(mHeight);
	if (v.empty()) return -1;

	int res = INF;
	for (Data& d : v)
		res = min(res, d.maxL);
	return res;
}

// 두 막대기 조합이 중복되지 않으면 true
bool isPossible(int a[], int b[]) {
	for (int i = 0; i < 3; i++) {
		if (a[i] == 0) continue;
		for (int j = 0; j < 3; j++)
			if (a[i] == b[j]) return 0;
	}
	return 1;
}

int requireTwin(int mHeight){
	setEntry(mHeight);

	int res = INF;
	for (Data& d1 : v)
		for (Data& d2 : v)
			if (isPossible(d1.id, d2.id))
				res = min(res, max(d1.maxL, d2.maxL));
	return res == INF? -1:res;
}
```
