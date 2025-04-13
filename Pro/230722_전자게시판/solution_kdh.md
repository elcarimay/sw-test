```cpp
#if 1
// erase에서 댓글 삭제시 가장 최상위 글에 대한 포인트를 반환
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <unordered_map>
#include <string>
#include <set>
using namespace std;
using pii = pair<int, int>;

#define MAXL 10
struct User {
	char name[13];
	int point;
}user[10003];

struct Message {
	int type, mid, uid, point, totPoint, pid; // type: 0 작성글, 1 댓글,답글
	vector<int> child;
}msg[50003];
unordered_map<string, int> uMap;
unordered_map<int, int> mMap;

int getUID(char c[]) {
	return uMap.count(c) ? uMap[c] : uMap[c] = uMap.size();
}
int getMID(int c) {
	return mMap.count(c) ? mMap[c] : mMap[c] = mMap.size();
}
void init() {
	uMap.clear(), mMap.clear();
	for (int i = 0; i < 10000; i++) user[i] = {};
	for (int i = 0; i < 50000; i++) msg[i] = {};
}

int writeMessage(char mUser[], int mID, int mPoint) {
	int uid = getUID(mUser), mid = getMID(mID);
	strcpy(user[uid].name, mUser);
	user[uid].point += mPoint;
	msg[mid] = { 0, mID, uid, mPoint, mPoint, -1 };
	return user[uid].point;
}

int update_parent(int id, int point) {
	while (msg[id].pid != -1) {
		msg[id].totPoint += point;
		user[msg[id].uid].point += point;
		id = msg[id].pid;
	}
	msg[id].totPoint += point;
	user[msg[id].uid].point += point;
	return id;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint) {
	int uid = getUID(mUser), mid = getMID(mID);
	int tid = getMID(mTargetID);
	strcpy(user[uid].name, mUser);
	msg[mid] = { 1, mID, uid, mPoint, mPoint, tid };
	msg[tid].child.push_back(mid);
	int root = update_parent(tid, mPoint);
	return msg[root].totPoint;
}

void update_child(int mid) {
	for (int nx : msg[mid].child) {
		auto& m = msg[nx];
		user[m.uid].point -= m.point;
		m.point = 0;
	}
}

int erase(int mID) {
	int mid = getMID(mID), ret, root;
	ret = msg[mid].totPoint;
	if (msg[mid].type) update_child(mid);
	root = update_parent(mid, -ret);
	return msg[root].totPoint;
}

struct BestMessage {
	int point, mid;
	bool operator<(const BestMessage& r)const {
		if (point != r.point) return point > r.point;
		return mid < r.mid;
	}
};
set<BestMessage> m;
struct BestUser {
	int point;
	char name[13];
	bool operator<(const BestUser& r)const {
		if (point != r.point) return point > r.point;
		return strcmp(name, r.name) < 0;
	}
};
set<BestUser> u;
void getBestMessages(int mBestMessageList[]) {
	m.clear();
	int cnt = 0;
	for (auto nx : mMap) {
		int mid = nx.second;
		if (msg[mid].type || !msg[mid].totPoint) continue;
		m.insert({ msg[mid].totPoint, msg[mid].mid });
		if (++cnt == 5) break;
	}
	set<BestMessage>::iterator it = m.begin();
	for (int i = 0; i < 5;i++) mBestMessageList[i] = it++ ->mid;
}

void getBestUsers(char mBestUserList[][MAXL + 1]) {
	u.clear();
	int cnt = 0; BestUser tmp;
	for (auto nx : uMap) {
		int uid = nx.second;
		if (!user[uid].point) continue;
		tmp.point = user[uid].point;
		strcpy(tmp.name, user[uid].name);
		u.insert(tmp);
		if (++cnt == 5) break;
	}
	set<BestUser>::iterator it = u.begin();
	for (int i = 0; i < 5; i++) strcpy(mBestUserList[i], it++->name);
}
#endif // 1

```
