```cpp
#define MAX_N 50000
#define MAX_M 50000
#define MAX_LEN 11
#include <unordered_map>
#include <algorithm>
#include <string.h>
#include <string>
#include <vector>
#include <set>
using namespace std;

set<char*> s[26];
unordered_map<int, int> m; // hash, id
char w[MAX_M][MAX_LEN];
int N, M, pid;
bool valid[MAX_M];

int getHash(char w[]) {
	int key = 0;
	for (int i = 0; i < strlen(w);i++)
		key = key * 26 + w[i] - 'a';
	return key;
}

void init(int N, int M, char mWords[][MAX_LEN]){
	::N = N, ::M = M; m.clear();
	for (int i = 0; i < 26;i++) s[i].clear();
	for (int i = 0;i < M;i++) {
		s[mWords[i][0] - 'a'].insert(mWords[i]);
		m[getHash(mWords[i])] = i;
		strcpy(w[i], mWords[i]);
	}
}

char tmp[MAX_LEN], ch;
int playRound(int mID, char mCh){
	pid = mID; ch = mCh;
	fill(valid, valid + M, 0);
	strcpy(tmp, *s[ch - 'a'].begin());
	int wid = m[getHash(tmp)];
		
	if (s[tmp[0] - 'a'].size() != 0 && !valid[wid]) {
		strcpy(tmp, *s[ch - 'a'].begin());
		wid = m[getHash(tmp)];
		valid[wid] = 1;
		ch = tmp[strlen(tmp)];
	}
	else {
		for (int i = 0;i < M;i++) {
			if(valid[i]) 
		}
	}
	pid = (pid + 1) % (N + 1);
		
	return 0;
}


```
