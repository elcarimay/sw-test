```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <set>
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;

#define MAX_L				(8)
#define ll long long int
#define REMOVED 0
#define LIVE 1

struct RESULT {
	int success;
	char word[MAX_L + 1];
};

struct Info {
	int state;
	char word[MAX_L + 1];
}info[40003];

vector<ll> word, prefix[30];
unordered_map<string, int> wmap; // string, idx
unordered_map<ll, int> hmap; // hash, string
int wordCnt;

ll getHash(char w[MAX_L]) {
	ll ret = 0; int len = strlen(w);
	int idx = len;
	for (int i = 8; 0 < i; i--)
		if (len > 0) ret += (27 - (w[idx - len--] - 'a' + 1)) * pow(27, i);
		else ret += 27 * pow(27, i);
	return ret;
}

void init(int N, char mWordList[][MAX_L + 1]) {
	for (int i = 0; i < 27; i++) prefix[i].clear();
	word.clear(); wordCnt = 0; wmap.clear(); hmap.clear();
	for (int i = 0; i < N; i++) {
		wmap[mWordList[i]] = wordCnt;
		ll h = getHash(mWordList[i]);
		word.push_back(h);
		prefix[mWordList[i][0] - 'a'].push_back(h);
		info[wordCnt].state = LIVE;
		strcpy(info[wordCnt].word, mWordList[i]);
		hmap[h] = wordCnt++;
	}
	sort(word.begin(), word.end(), greater<ll>());
	for (int i = 0; i < 27; i++) {
		ll h = getHash(mWordList[i]);
		auto& p = prefix[i];
		sort(p.begin(), p.end(), greater<ll>());
	}
}

int add(char mWord[]) {
	auto it = wmap.find(mWord);
	if (it != wmap.end()) return 0;
	int wid = wmap[mWord] = wordCnt++;
	ll h = getHash(mWord);
	word.push_back(h);
	hmap[h] = wid;
	info[wid].state = LIVE;
	strcpy(info[wid].word, mWord);
	sort(word.begin(), word.end(), greater<ll>());
	auto& p = prefix[mWord[0] - 'a'];
	p.push_back(h);
	sort(p.begin(), p.end(), greater<ll>());
	return 1;
}

int erase(char mWord[]) {
	auto it = wmap.find(mWord);
	if (it == wmap.end()) return 0;
	int wid = it->second;
	if (info[wid].state == REMOVED) return 0;
	info[wid] = { REMOVED, "" };
	ll h = getHash(mWord);
	for (int i = 0; i < word.size(); i++) if (word[i] == h) word.erase(word.begin() + i);
	auto& p = prefix[mWord[0] - 'a'];
	for (int i = 0; i < p.size(); i++) if (p[i] == h) p.erase(p.begin() + i);
	hmap.erase(hmap.find(h));
	return 1;
}

RESULT find(char mInitial, int mIndex) {
	RESULT res = { 0,"" };
	auto& p = prefix[mInitial - 'a'];
	int order = 1;
	for (int i = 0; i < p.size(); i++) {
		int wid = hmap[p[i]];
		if (info[wid].state == REMOVED) continue;
		if (order == mIndex)
			res.success = 1, strcpy(res.word, info[wid].word);
		order++;
	}
	return res;
}

int getIndex(char mWord[]) {
	ll h = getHash(mWord);
	auto it = wmap.find(mWord);
	if (it == wmap.end()) return 0;
	if (info[it->second].state == REMOVED) return 0;
	int order = 1;
	for (int i = 0; i < word.size(); i++) {
		int wid = hmap[word[i]];
		if (info[wid].state == REMOVED) continue;
		if (word[i] == h) return order;
		order++;
	}
}
```
