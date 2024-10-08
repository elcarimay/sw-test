```cpp
#define MAX_N 50000
#define MAX_M 50000
#define MAX_LEN 11
#include <string>
#include <list>
#include <unordered_set>
#include <algorithm>
#include <queue>
using namespace std;

unordered_set<string> used;
priority_queue<string, vector<string>, greater<>> pq[26];
list<int> li;
list<int>::iterator iter[50003];

void init(int N, int M, char mWords[][MAX_LEN]){
	used.clear();
	li.clear();
	for (int i = 1; i <= N; i++) iter[i] = li.insert(li.end(), i);
	for (int i = 0; i < 26; i++) pq[i] = {};
	for (int i = 0; i < M; i++) {
		pq[mWords[i][0] - 'a'].push(mWords[i]);
		used.insert(mWords[i]);
	}
}

vector<string> backup;
int playRound(int mID, char mCh){
	auto it = iter[mID];
	backup.clear();
	while (pq[mCh - 'a'].size()) {
		string str = pq[mCh - 'a'].top(); pq[mCh - 'a'].pop();
		mCh = str.back();
		reverse(str.begin(), str.end());
		backup.push_back(str);
		++it;
		if (it == li.end()) it = li.begin();
	}
	for (auto& str : backup) {
		if (!used.count(str)) {
			pq[str[0] - 'a'].push(str);
			used.insert(str);
		}
	}
	int ret = *it;
	li.erase(it);
	return ret;
}
```
