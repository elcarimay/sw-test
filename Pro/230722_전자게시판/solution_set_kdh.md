```cpp
#if 1
// erase에서 댓글 삭제시 가장 최상위 글에 대한 포인트를 반환
// best Message나 best User를 할때 단순히 5개만 뽑으면 안되고 첫번째가 같더라도 두번째가 다른게 있기 때문에 set으로 구현시 실시간 update가 필요함
// 동일한 mid로 다른 글이 쓰여질수 있기 때문에 getID함수를 만들때 주의해야 함
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <unordered_map>
#include <string>
#include <set>
using namespace std;
#define MAXL 10

struct User {
	char name[13];
	int point;
}user[10003];

struct Message {
	int type, mid, uid, point, totPoint, pid; // type: 0 작성글, 1 댓글, 답글
	vector<int> child;
}msg[50003];
unordered_map<string, int> uMap;
unordered_map<int, int> mMap;
int getUID(char c[]) {
	if (uMap.count(c)) return uMap[c];
	int uid = uMap.size() + 1;
	strcpy(user[uid].name, c);
	user[uid].point = 0;
	return uMap[c] = uid;
}

struct BestMessage {
	int mid;
	bool operator<(const BestMessage& r)const {
		if (msg[mid].totPoint != msg[r.mid].totPoint) return msg[mid].totPoint > msg[r.mid].totPoint;
		return msg[mid].mid < msg[r.mid].mid;
	}
};
set<BestMessage> m;
struct BestUser {
	int uid;
	bool operator<(const BestUser& r)const {
		if (user[uid].point != user[r.uid].point) return user[uid].point > user[r.uid].point;
		return strcmp(user[uid].name, user[r.uid].name) < 0;
	}
};
set<BestUser> u;
void init(){
	uMap.clear(), mMap.clear(), m.clear(), u.clear();
}

void updateUser(int uid, int point) {
	u.erase({ uid });
	user[uid].point += point;
	u.insert({ uid });
}

int writeMessage(char mUser[], int mID, int mPoint){
	int uid = getUID(mUser), mid = mMap[mID] = mMap.size() + 1;
	strcpy(user[uid].name, mUser);
	updateUser(uid, mPoint);
	msg[mid] = { 0, mID, uid, mPoint, mPoint};
	m.insert({ mid });
	return user[uid].point;
}

int update_parent(int pid, int point) {
	if (msg[pid].type) {
		msg[pid].totPoint += point;
		pid = msg[pid].pid;
	}
	m.erase({ pid });
	msg[pid].totPoint += point;
	m.insert({ pid });
	return pid;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint){
	int uid = getUID(mUser), mid = mMap[mID] = mMap.size() + 1;
	int tid = mMap[mTargetID];
	strcpy(user[uid].name, mUser);
	updateUser(uid, mPoint);
	msg[mid] = { 1, mID, uid, mPoint, mPoint, tid };
	msg[tid].child.push_back(mid);
	int root = update_parent(tid, mPoint);
	return msg[root].totPoint;
}

void updateUserAll(int mid) {
	updateUser(msg[mid].uid, -msg[mid].point);
	for (int cid : msg[mid].child) updateUserAll(cid);
}

int erase(int mID){
	int mid = mMap[mID], root;
	int pid = msg[mid].pid;
	updateUserAll(mid); // 자식노드는 user point를 update
	if (!msg[mid].type) {
		m.erase({ mid }); // 메세지는 set에서 삭제
		return user[msg[mid].uid].point;
	}
	msg[pid].child.erase(find(msg[pid].child.begin(), msg[pid].child.end(), mid)); // 댓글은 부모에서 자식을 삭제
	root = update_parent(pid, -msg[mid].totPoint); // 부모노드는 totPoint를  update
	return msg[root].totPoint;
}

void getBestMessages(int mBestMessageList[]){
	int cnt = 0;
	for (auto nx : m) {
		mBestMessageList[cnt++] = msg[nx.mid].mid;
		if (cnt == 5) break;
	}
}

void getBestUsers(char mBestUserList[][MAXL + 1]){
	int cnt = 0;
	for (auto nx : u) {
		strcpy(mBestUserList[cnt++], user[nx.uid].name);
		if (cnt == 5) break;
	}
}
#endif // 0
```
