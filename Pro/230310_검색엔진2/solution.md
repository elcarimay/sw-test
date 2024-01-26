```cpp
#include <unordered_map>
#include <map>
#include <vector>
#include <queue>
#include <string>
using namespace std;

unordered_map<string, int> wordHash;

const int NOT_RELATED = -1;

struct Result
{
	int mOrder, mRank;
};

struct Word
{
	string str;
	int count, group_id;
};

struct Group
{
	vector<int> wordList;
	int count;
};

vector<Word> DB;

struct Data
{
	int count, id;
	bool operator<(const Data& data)const {
		return (count == data.count && DB[data.id].str < DB[id].str) ||
			(count < data.count);
	}
};

vector<Group> groupList;
vector<int> pref1[26], pref2[26][26], pref3[26][26][26];

void init(){
	wordHash.clear(); DB.clear(); groupList.clear();
	for (int i = 0; i < 26; i++)
	{
		pref1[i].clear();
		for (int j = 0; j < 26; j++)
		{
			pref2[i][j].clear();
			for (int k = 0; k < 26; k++) pref3[i][j][k].clear();
		}
	}
	return;
}

void updateCount(int word_id, int mCount) {
	if (DB[word_id].group_id == NOT_RELATED) DB[word_id].count += mCount;
	else groupList[DB[word_id].group_id].count += mCount;
}

int getCount(int word_id) {
	if (DB[word_id].group_id == NOT_RELATED) return DB[word_id].count;
	else return groupList[DB[word_id].group_id].count;
}

void search(char mStr[], int mCount){
	string str(mStr);
	auto res = wordHash.find(str);
	if (res == wordHash.end()) {
		Word newWord = { str, mCount, NOT_RELATED };
		int id = DB.size();
		DB.push_back(newWord);
		pref1[str[0] - 'a'].push_back(id);
		pref2[str[0] - 'a'][str[1] - 'a'].push_back(id);
		if (str.length() >= 3) pref3[str[0] - 'a'][str[1] - 'a'][str[2] - 'a'].push_back(id);
		wordHash[str] = id;
	}
	else updateCount(res->second, mCount);
	return;
}

bool myStrCmp(string& db, string& str, int size) {
	if (db.size() < size) return false;
	for (int i = 0; i < size; i++)
		if (db[i] != str[i]) return false;
	return true;
}

bool myStrCmp(string& db, string& str) {
	if (db.size() != str.size()) return false;
	for (int i = 0; i < str.size(); i++)
		if (db[i] != str[i]) return false;
	return true;
}

Result recommend(char mStr[]){
	string str(mStr);
	for (int i = 0; i <= str.size(); i++)
	{
		priority_queue<Data> Q;
		for (int j = 0; j < DB.size(); j++)
		{
			if (!myStrCmp(DB[j].str, str, i)) continue;
			Q.push({ getCount(j), j });
		}
		int cnt = 0;
		while (!Q.empty() && cnt < 5) {
			cnt++;
			auto cur = Q.top(); Q.pop();
			if (!myStrCmp(DB[cur.id].str, str)) continue;
			updateCount(cur.id, 1);
			return { i, cnt };
		}
	}
	return { 0, 0 };
}


int relate(char mStr1[], char mStr2[]){
	int wid1 = wordHash[string(mStr1)];
	int wid2 = wordHash[string(mStr2)];

	int gid1 = DB[wid1].group_id;
	int gid2 = DB[wid2].group_id;

	if (gid1 == NOT_RELATED && gid2 == NOT_RELATED) {
		Group newGroup;
		newGroup.count = DB[wid1].count + DB[wid2].count;
		newGroup.wordList.push_back(wid1);
		newGroup.wordList.push_back(wid2);

		gid1 = groupList.size();
		DB[wid1].group_id = DB[wid2].group_id = gid1;

		groupList.push_back(newGroup);
		return groupList[gid1].count;
	}
	else if (gid1 == NOT_RELATED || gid2 == NOT_RELATED) {
		if (gid1 != NOT_RELATED) {
			groupList[gid1].wordList.push_back(wid2);
			DB[wid2].group_id = gid1;
			groupList[gid1].count += DB[wid2].count;
			return groupList[gid1].count;
		}
		else {
			groupList[gid2].wordList.push_back(wid1);
			DB[wid1].group_id = gid2;
			groupList[gid2].count += DB[wid1].count;
			return groupList[gid2].count;
		}
	}
	else {
		if (groupList[gid1].wordList.size() > groupList[gid2].wordList.size()) {
			groupList[gid1].count += groupList[gid2].count;
			for (auto g : groupList[gid2].wordList)
				DB[g].group_id = gid1;
			return groupList[gid1].count;
		}
		else {
			groupList[gid2].count += groupList[gid1].count;
			for (auto g : groupList[gid1].wordList)
				DB[g].group_id = gid2;
			return groupList[gid2].count;
		}
	}
	return 0;
}

void rank(char mPrefix[], int mRank, char mReturnStr[]){
	priority_queue<Data> Q;

	if (mPrefix[1] == '\0') {
		for (auto w : pref1[mPrefix[0] - 'a']) Q.push({ getCount(w), w });
	}
	else if (mPrefix[2] == '\0') {
		for (auto w : pref2[mPrefix[0] - 'a'][mPrefix[1] - 'a']) Q.push({ getCount(w), w });
	}
	else {
		for (auto w : pref3[mPrefix[0] - 'a'][mPrefix[1] - 'a'][mPrefix[2] - 'a'])
			Q.push({ getCount(w),w });
	}
	for (int i = 1; i < mRank; i++) Q.pop();

	auto w = Q.top();
	for (int i = 0; i < DB[w.id].str.size(); i++) mReturnStr[i] = DB[w.id].str[i];
	mReturnStr[DB[w.id].str.size()] = 0;
	return;
}
```
