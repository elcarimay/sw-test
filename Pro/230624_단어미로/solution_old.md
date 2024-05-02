```cpp
#if 0 // 최적화 1 866ms
#define _CRT_SECURE_NO_WARNINGS
#include <map>
#include <unordered_map>
#include <string>
#include <vector>
using namespace std;

struct Word
{
	string str, pattern[3];
};

struct Data
{
	string str;
	int id;
};

Word word[30001];
unordered_map<string, int> word2id;
unordered_map<string, vector<Data>> front2;
unordered_map<string, vector<Data>> front4;
unordered_map<string, vector<Data>> mid;
unordered_map<string, vector<Data>> back2;
unordered_map<string, vector<Data>> back4;

int current;

void init(){
	word2id.clear(); front2.clear(); front4.clear();
	mid.clear(); back2.clear(); back4.clear();
}

// addRoom() 함수의 호출 횟수는 최대 30,000 이다.
void addRoom(int mID, char mWord[], int mDirLen[]){
	string w = string(mWord);
	word2id[w] = mID;
	word[mID].str = w;
	word[mID].pattern[0] = mDirLen[0] == 2 ? w.substr(0, 2) : w.substr(0, 4);
	word[mID].pattern[1] = w.substr(4, 3);
	word[mID].pattern[2] = mDirLen[2] == 2 ? w.substr(9, 2) : w.substr(7, 4);

	front2[w.substr(0, 2)].push_back({ w,mID });
	front4[w.substr(0, 4)].push_back({ w,mID });
	mid[w.substr(4, 3)].push_back({ w,mID });
	back2[w.substr(9, 2)].push_back({ w,mID });
	back4[w.substr(7, 4)].push_back({ w,mID });

}

// setCurrent() 함수의 호출 횟수는 최대 500 이다.
void setCurrent(char mWord[]){
	current = word2id.find(string(mWord))->second;
}

int search(vector<Data>& list) {
	int res = 0;
	for (auto d:list)
	{
		if (d.id == current) continue;
		if (d.str != word[d.id].str) continue;
		if (res == 0) res = d.id;
		else if (word[res].str > word[d.id].str) res = d.id;
	}
	if (res != 0) current = res;
	return res;
}

// moveDir() 함수의 호출 횟수는 최대 50,000 이다.
int moveDir(int mDir){
	if (mDir == 0) {
		if (word[current].pattern[0].size() == 2) return search(back2[word[current].pattern[0]]);
		return search(back4[word[current].pattern[0]]);
	}
	else if (mDir == 1) return search(mid[word[current].pattern[1]]);
	else {
		if (word[current].pattern[2].size() == 2) return search(front2[word[current].pattern[2]]);
		return search(front4[word[current].pattern[2]]);
	}
	return 0;
}

// changeWord() 함수의 호출 횟수는 최대 3,000 이다.
void changeWord(char mWord[], char mChgWord[], int mChgLen[]){
	addRoom(word2id.find(string(mWord))->second, mChgWord, mChgLen);
}
#endif

#if 0 // 최적화 2 619 ms
#define _CRT_SECURE_NO_WARNINGS
#include <map>
#include <unordered_map>
#include <string>
#include <vector>
#include <queue>
using namespace std;

struct Word
{
	string str, pattern[3];
};

struct Data
{
	string str;
	int id;
	bool operator<(const Data& data)const {
		return str > data.str;
	}
};

Word word[30001];
unordered_map<string, int> word2id;
unordered_map<string, priority_queue<Data>> front2;
unordered_map<string, priority_queue<Data>> front4;
unordered_map<string, priority_queue<Data>> mid;
unordered_map<string, priority_queue<Data>> back2;
unordered_map<string, priority_queue<Data>> back4;

int current;

void init() {
	word2id.clear(); front2.clear(); front4.clear();
	mid.clear(); back2.clear(); back4.clear();
}

// addRoom() 함수의 호출 횟수는 최대 30,000 이다.
void addRoom(int mID, char mWord[], int mDirLen[]) {
	string w = string(mWord);
	word2id[w] = mID;
	word[mID].str = w;
	word[mID].pattern[0] = mDirLen[0] == 2 ? w.substr(0, 2) : w.substr(0, 4);
	word[mID].pattern[1] = w.substr(4, 3);
	word[mID].pattern[2] = mDirLen[2] == 2 ? w.substr(9, 2) : w.substr(7, 4);

	front2[w.substr(0, 2)].push({ w,mID });
	front4[w.substr(0, 4)].push({ w,mID });
	mid[w.substr(4, 3)].push({ w,mID });
	back2[w.substr(9, 2)].push({ w,mID });
	back4[w.substr(7, 4)].push({ w,mID });

}

// setCurrent() 함수의 호출 횟수는 최대 500 이다.
void setCurrent(char mWord[]) {
	current = word2id.find(string(mWord))->second;
}

int myStrcmp(string& a, string& b) {
	int len = min(a.size(), b.size());
	for (int i = 0; i < len; i++)
	{
		int d = a[i] - b[i];
		if (d != 0) return d;
	}
	if (a.size() > b.size()) return 1;
	return 0;
}

int search(priority_queue<Data>& Q) {
	int res = 0;
	vector<int> popped;
	
	while (Q.size()) {
		auto d = Q.top(); Q.pop();
		if (d.str != word[d.id].str) continue;
		popped.push_back(d.id);
		if (d.id == current) continue;
		current = res = d.id;
		break;
	}
	for (auto i : popped) Q.push({ word[i].str,i });
	return res;
}

// moveDir() 함수의 호출 횟수는 최대 50,000 이다.
int moveDir(int mDir) {
	if (mDir == 0) {
		if (word[current].pattern[0].size() == 2) return search(back2[word[current].pattern[0]]);
		return search(back4[word[current].pattern[0]]);
	}
	else if (mDir == 1) return search(mid[word[current].pattern[1]]);
	else {
		if (word[current].pattern[2].size() == 2) return search(front2[word[current].pattern[2]]);
		return search(front4[word[current].pattern[2]]);
	}
	return 0;
}

// changeWord() 함수의 호출 횟수는 최대 3,000 이다.
void changeWord(char mWord[], char mChgWord[], int mChgLen[]) {
	addRoom(word2id.find(string(mWord))->second, mChgWord, mChgLen);
}
#endif

#if 0 // 최적화 3 325 ms
#define _CRT_SECURE_NO_WARNINGS
#include <map>
#include <unordered_map>
#include <string>
#include <vector>
#include <queue>
using namespace std;

typedef long long LL;

struct Word
{
	LL str, pattern[3];
	int len[3];
};

struct Data
{
	LL str;
	int id;
	bool operator<(const Data& data)const {
		return str > data.str;
	}
};

Word word[30001];
unordered_map<string, int> word2id;
unordered_map<int, priority_queue<Data>> front2;
unordered_map<int, priority_queue<Data>> front4;
unordered_map<int, priority_queue<Data>> mid;
unordered_map<int, priority_queue<Data>> back2;
unordered_map<int, priority_queue<Data>> back4;

int current;

void init() {
	word2id.clear(); front2.clear(); front4.clear();
	mid.clear(); back2.clear(); back4.clear();
}

LL str2Code(string& a) {
	LL res = 0;
	for (auto c:a)
	{
		res += (c - 'a');
		/*printf("%c\n", c);
		printf("%ld\n", res);*/
		res *= 26;
	}
	return res / 26;
}

int str2Code(string& a, int s, int len) {
	int res = 0;
	for (int i = 0; i < len; i++)
	{
		res += (a[s + i] - 'a');
		res *= 26;
	}
	return res / 26;
}



// addRoom() 함수의 호출 횟수는 최대 30,000 이다.
void addRoom(int mID, char mWord[], int mDirLen[]) {
	string w = string(mWord);
	word2id[w] = mID;

	LL wordCode = str2Code(w);
	word[mID].str = wordCode;

	int w02 = str2Code(w, 0, 2);
	int w04 = str2Code(w, 0, 4);
	int w43 = str2Code(w, 4, 3);
	int w92 = str2Code(w, 9, 2);
	int w74 = str2Code(w, 7, 4);

	word[mID].pattern[0] = mDirLen[0] == 2 ? w02 : w04;
	word[mID].pattern[1] = w43;
	word[mID].pattern[2] = mDirLen[2] == 2 ? w92 : w74;
	word[mID].len[0] = mDirLen[0];
	word[mID].len[1] = mDirLen[1];
	word[mID].len[2] = mDirLen[2];

	front2[w02].push({ wordCode,mID });
	front4[w04].push({ wordCode,mID });
	mid[w43].push({ wordCode,mID });
	back2[w92].push({ wordCode,mID });
	back4[w74].push({ wordCode,mID });
}

// setCurrent() 함수의 호출 횟수는 최대 500 이다.
void setCurrent(char mWord[]) {
	current = word2id.find(string(mWord))->second;
}

int search(priority_queue<Data>& Q) {
	int res = 0;
	vector<int> popped;

	while (Q.size()) {
		auto d = Q.top(); Q.pop();
		if (d.str != word[d.id].str) continue;
		popped.push_back(d.id);
		if (d.id == current) continue;
		current = res = d.id;
		break;
	}
	for (auto i : popped) Q.push({ word[i].str,i });
	return res;
}

// moveDir() 함수의 호출 횟수는 최대 50,000 이다.
int moveDir(int mDir) {
	if (mDir == 0) {
		if (word[current].len[mDir] == 2) return search(back2[word[current].pattern[0]]);
		return search(back4[word[current].pattern[0]]);
	}
	else if (mDir == 1) return search(mid[word[current].pattern[1]]);
	else {
		if (word[current].len[mDir] == 2) return search(front2[word[current].pattern[2]]);
		return search(front4[word[current].pattern[2]]);
	}
	return 0;
}

// changeWord() 함수의 호출 횟수는 최대 3,000 이다.
void changeWord(char mWord[], char mChgWord[], int mChgLen[]) {
	addRoom(word2id.find(string(mWord))->second, mChgWord, mChgLen);
}
#endif

#if 1 // 최적화 423  ms
#define _CRT_SECURE_NO_WARNINGS
#include <map>
#include <unordered_map>
#include <string>
#include <vector>
#include <queue>
using namespace std;

typedef long long LL;

struct Word
{
	LL str, pattern[3];
	int len[3];
};

struct Data
{
	LL str;
	int id;
	bool operator<(const Data& data)const {
		return str > data.str;
	}
};

Word word[30001];
unordered_map<string, int> word2id;
priority_queue<Data> front2[26 * 26];
priority_queue<Data> front4[26 * 26 * 26 * 26];
priority_queue<Data> mid[26 * 26 * 26];
priority_queue<Data> back2[26 * 26];
priority_queue<Data> back4[26 * 26 * 26 * 26];
int current;

void clearQueue(priority_queue<Data>& Q) {
	while (!Q.empty()) Q.pop();
}

void init() {
	word2id.clear();
	for (int i = 0; i < 26*26*26*26; i++)
	{
		clearQueue(front4[i]); clearQueue(back4[i]);
		if (i < 26 * 26 * 26) clearQueue(mid[i]);
		if (i < 26 * 26) {
			clearQueue(front2[i]); clearQueue(back2[i]);
		}
	}
}

LL str2Code(string& a) {
	LL res = 0;
	for (auto c : a)
	{
		res += (c - 'a');
		res *= 26;
	}
	return res / 26;
}

int str2Code(string& a, int s, int len) {
	int res = 0;
	for (int i = 0; i < len; i++)
	{
		res += (a[s + i] - 'a');
		res *= 26;
	}
	return res / 26;
}

// addRoom() 함수의 호출 횟수는 최대 30,000 이다.
void addRoom(int mID, char mWord[], int mDirLen[]) {
	string w = string(mWord);
	word2id[w] = mID;

	LL wordCode = str2Code(w);
	word[mID].str = wordCode;

	int w02 = str2Code(w, 0, 2);
	int w04 = str2Code(w, 0, 4);
	int w43 = str2Code(w, 4, 3);
	int w92 = str2Code(w, 9, 2);
	int w74 = str2Code(w, 7, 4);

	word[mID].pattern[0] = mDirLen[0] == 2 ? w02 : w04;
	word[mID].pattern[1] = w43;
	word[mID].pattern[2] = mDirLen[2] == 2 ? w92 : w74;
	word[mID].len[0] = mDirLen[0];
	word[mID].len[1] = mDirLen[1];
	word[mID].len[2] = mDirLen[2];

	front2[w02].push({ wordCode,mID });
	front4[w04].push({ wordCode,mID });
	mid[w43].push({ wordCode,mID });
	back2[w92].push({ wordCode,mID });
	back4[w74].push({ wordCode,mID });
}

// setCurrent() 함수의 호출 횟수는 최대 500 이다.
void setCurrent(char mWord[]) {
	current = word2id.find(string(mWord))->second;
}

int search(priority_queue<Data>& Q) {
	int res = 0;
	vector<int> popped;

	while (Q.size()) {
		auto d = Q.top(); Q.pop();
		if (d.str != word[d.id].str) continue;
		popped.push_back(d.id);
		if (d.id == current) continue;
		current = res = d.id;
		break;
	}
	for (auto i : popped) Q.push({ word[i].str,i });
	return res;
}

// moveDir() 함수의 호출 횟수는 최대 50,000 이다.
int moveDir(int mDir) {
	if (mDir == 0) {
		if (word[current].len[mDir] == 2) return search(back2[word[current].pattern[0]]);
		return search(back4[word[current].pattern[0]]);
	}
	else if (mDir == 1) return search(mid[word[current].pattern[1]]);
	else {
		if (word[current].len[mDir] == 2) return search(front2[word[current].pattern[2]]);
		return search(front4[word[current].pattern[2]]);
	}
	return 0;
}

// changeWord() 함수의 호출 횟수는 최대 3,000 이다.
void changeWord(char mWord[], char mChgWord[], int mChgLen[]) {
	addRoom(word2id.find(string(mWord))->second, mChgWord, mChgLen);
}
#endif
```
