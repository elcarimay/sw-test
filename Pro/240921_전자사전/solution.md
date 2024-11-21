```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <set>
#include <vector>
#include <string>
#include <unordered_map>
using namespace std;

#define MAX_L				(8)
#define ll long long int
#define REMOVED 0
#define LIVE 1

struct RESULT{
	int success;
	char word[MAX_L + 1];
};

struct Word {
	ll hash_value;
	int state;
	bool operator<(const Word& r)const {
		return hash_value > r.hash_value;
	}
};

set<Word>::iterator word_iter[40003];
set<Word> word;
set<Word>::iterator prefix_iter[40003];
set<Word> prefix[30];
unordered_map<string, int> wmap; // string, idx
unordered_map<ll, string> hashmap; // hash, string
int wordCnt;

ll getHash(char w[MAX_L]) {
	ll ret = 0; int len = strlen(w);
	int idx = len;
	for (int i = 8; 0 < i; i--) {
		if (len > 0) {
			int a = w[idx - len--] - 'a'; a++;
			ret += (27 - a) * pow(27, i);
		}
		else {
			int a = 27 * pow(27, i);
			ret += a;
		}
	}
	return ret;
}

void init(int N, char mWordList[][MAX_L + 1]){
	for (int i = 0; i < 27; i++) prefix[i].clear();
	word.clear(); wordCnt = 0; wmap.clear(); hashmap.clear();
	for (int i = 0; i < N; i++) {
		wmap[mWordList[i]] = wordCnt;
		ll h = getHash(mWordList[i]);
		word_iter[wordCnt] = word.insert({ h, LIVE }).first;
		prefix_iter[wordCnt++] = prefix[mWordList[i][0] - 'a'].insert({ h, LIVE }).first;
		hashmap[h] = mWordList[i];
	}
}

int add(char mWord[]) {
	if (wmap.count(mWord)) return 0;
	int wid = wmap[mWord];
	ll h = getHash(mWord);
	word_iter[wid] = word.insert({ h, LIVE }).first;
	prefix_iter[wid] = prefix[mWord[0] - 'a'].insert({h, LIVE}).first;
	hashmap[h] = mWord;
	return 1;
}

int erase(char mWord[]){
	if (!wmap.count(mWord)) return 0;
	int wid = wmap[mWord];
	if (word_iter[wid]->state == REMOVED) return 0;
	ll h = getHash(mWord);
	word.erase(word_iter[wid]);
	word_iter[wid] = word.insert({ h, REMOVED }).first;
	prefix[mWord[0] - 'a'].erase(prefix_iter[wid]);
	prefix_iter[wid] = prefix[mWord[0] - 'a'].insert({ h, REMOVED }).first;
	hashmap.erase(hashmap.find(h));
	return 1;
}

RESULT find(char mInitial, int mIndex){
	RESULT res;
	res.success = 0;
	res.word[0] = '\0';
	auto it = prefix[mInitial - 'a'].begin();
	int order = 1;
	for (; it != prefix[mInitial - 'a'].end(); it++) {
		if (it->state == REMOVED) continue;
		if (order == mIndex) {
			res.success = 1;
			strcpy(res.word, hashmap[it->hash_value].c_str());
		}
		order++;
	}
	return res;
}

int getIndex(char mWord[]){
	int order = 1;
	ll h = getHash(mWord);
	auto it = wmap.find(mWord);
	if (it == wmap.end()) return 0;
	if (word_iter[it->second]->state == REMOVED) return 0;
	auto it1 = word.begin();
	for (; it1 != word.end(); it1++) {
		if (it1->state == REMOVED) continue;
		if (it1->hash_value == h) return order;
		order++;
	}
}
```
