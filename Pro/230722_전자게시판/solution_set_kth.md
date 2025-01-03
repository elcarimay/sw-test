```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <set>
#include <vector>
#include <algorithm>
#include <string>
#include <string.h>
using namespace std;
#define MAXL 10

unordered_map<string, int> umap;
unordered_map<int, int> mmap;
int uCnt, mCnt;

struct User {
	char name[13];
	int point;
}user[10003];

struct Message {
	int type, mID, uid, point, totPoint, parent; // type 0: 작성글, 1:댓글,답글
	vector<int> child;
	void eraseChild(int x) {
		child.erase(find(child.begin(), child.end(), x));
	}
}msg[50003];

struct UserComp {
	bool operator()(const int l, const int r) const {
		if (user[l].point != user[r].point) return user[l].point > user[r].point;
		return strcmp(user[l].name, user[r].name) < 0;
	}
};

struct MsgComp {
	bool operator()(const int l, const int r) const {
		if (msg[l].totPoint != msg[r].totPoint) return msg[l].totPoint > msg[r].totPoint;
		return msg[l].mID < msg[r].mID;
	}
};

set<int, UserComp> uset;
set<int, MsgComp> mset;

void init(){
	uCnt = mCnt = 0;
	umap.clear(); mmap.clear(); uset.clear(); mset.clear();
}

int getuid(char s[]) {
	if (umap.find(s) != umap.end()) return umap[s];
	strcpy(user[uCnt].name, s);
	user[uCnt].point = 0;
	return umap[s] = uCnt++;
}

void updateUser(int uid, int p) {
	uset.erase(uid);
	user[uid].point += p;
	uset.insert(uid);
}

int updateMsg(int mid, int p) {
	if (msg[mid].type) {
		msg[mid].totPoint += p;
		mid = msg[mid].parent;
	}
	mset.erase(mid);
	msg[mid].totPoint += p;
	mset.insert(mid);
	return msg[mid].totPoint;
}

int writeMessage(char mUser[], int mID, int mPoint){
	int uid = getuid(mUser);
	int mid = mmap[mID] = mCnt++;
	updateUser(uid, mPoint);
	msg[mid] = { 0, mID, uid, mPoint, mPoint };
	mset.insert(mid);
	return user[uid].point;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint){
	int uid = getuid(mUser);
	int mid = mmap[mID] = mCnt++;
	int pid = mmap[mTargetID];

	updateUser(uid, mPoint);

	msg[mid] = { 1, mID, uid, mPoint, mPoint, pid };
	msg[pid].child.push_back(mid);
	return updateMsg(pid, mPoint);
}

void updateUserAll(int mid) {
	updateUser(msg[mid].uid, -msg[mid].point);
	for (int cid : msg[mid].child) updateUserAll(cid);
}

int erase(int mID){
	int mid = mmap[mID];
	int pid = msg[mid].parent;
	updateUserAll(mid);
	if (msg[mid].type == 0) {
		mset.erase(mid);
		return user[msg[mid].uid].point;
	}
	msg[pid].eraseChild(mid);
	return updateMsg(pid, -msg[mid].totPoint);
}

void getBestMessages(int mBestMessageList[]){
	int n = 0;
	for (int mid : mset) {
		mBestMessageList[n++] = msg[mid].mID;
		if (n == 5) break;
	}
	return;
}

void getBestUsers(char mBestUserList[][MAXL + 1]){
	int n = 0;
	for (int uid : uset) {
		strcpy(mBestUserList[n++], user[uid].name);
		if (n == 5) break;
	}
}
```
