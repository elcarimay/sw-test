```cpp
#if 1 // stl2: 0.4초(최적화 disabled)
// 전자게시판 실행속도(최적화 OFF)
// STL1: 8.9 초, STL2: 0.4 초
// manual1(MaxHeap 지역): 5.5 초, manual2(MaxHeap 전역): 0.4 초, manual3(MaxHeap 지역): 4.1 초

#define _CRT_SECURE_NO_WARNINGS
#define MAXL (10)
#include <vector>
#include <queue>
#include <unordered_map>
#include <string>
#include <cstring>
using namespace std;

#define MAX_USERS 10000 // 각 테스트 케이스에서 사용자의 수는 10,000 이하이다.
#define MAX_MESSAGES 50000 // 각 테스트 케이스에서 모든 함수의 호출 횟수는 50,000 이하이다.
#define ERASED 1

struct User
{
	char mUser[MAXL + 1]; // char의 경우 "abc" → "a, b, c, \n"와 같이 끝에 null문자가 하나 더 들어감
	int totPoint;
	User() { strcpy(this->mUser, ""); this->totPoint = 0; }
	User(const char mUser[], int totPoint) {
		strcpy(this->mUser, mUser);
		this->totPoint = totPoint;
	}
	bool operator<(const User& user) const {
		return (totPoint < user.totPoint) ||
			(totPoint == user.totPoint && strcmp(mUser, user.mUser) > 0);
		// 문자열을 비교할때 아스키코드로 비교하기 때문에 사전순서로 앞이면 음수, 뒤면 양수
	}
	bool operator==(const User& user) const {
		return totPoint == user.totPoint && strcmp(mUser, user.mUser) == 0;
	}
};
User users[MAX_USERS];
int userCnt;
unordered_map<string, int> userMap;

struct Message
{
	int mID, totPoint, mPoint, user, root, state;
	vector<int> childList;
};
Message msg[MAX_MESSAGES];
int msgCnt;
unordered_map<int, int> msgMap;
bool visited[MAX_MESSAGES];

struct MessageData
{
	int mID, totPoint;
	bool operator<(const MessageData& msg) const {
		return (totPoint < msg.totPoint) ||
			(totPoint == msg.totPoint && mID > msg.mID);
	}
	bool operator==(const MessageData& msg) const {
		return totPoint == msg.totPoint && mID == msg.mID;
	}
};

priority_queue<MessageData> msgPQ;
priority_queue<User> userPQ;

int get_userIndex(string mUser) {
	int uIdx;
	auto iter = userMap.find(mUser);
	if (iter == userMap.end()) { // unordered_map은 find로 못찾으면 end()를 반환함
		uIdx = userCnt++;
		userMap.insert({ mUser, uIdx });
	}
	else uIdx = iter->second;
	return uIdx;
}

int get_msgIndex(int mID) {
	int mIdx;
	auto iter = msgMap.find(mID);
	if (iter == msgMap.end()) { // unordered_map은 find로 못찾으면 end()를 반환함
		mIdx = msgCnt++;
		msgMap.insert({ mID, mIdx });
	}
	else mIdx = iter->second;
	return mIdx;
}

void init(){
	for (int i = 0; i < MAX_USERS; i++) users[i] = {};
	userCnt = 0; userMap.clear();
	for (int i = 0; i < MAX_MESSAGES; i++) msg[i] = {};
	msgCnt = 0; msgMap.clear();
	while (msgPQ.size()) { msgPQ.pop(); }
	while (userPQ.size()) { userPQ.pop(); }
}

int writeMessage(char mUser[], int mID, int mPoint){
	int uIdx = get_userIndex(mUser);
	int mIdx = get_msgIndex(mID);

	strcpy(users[uIdx].mUser, mUser);
	users[uIdx].totPoint += mPoint;
	userPQ.push({ mUser,users[uIdx].totPoint });

	msg[mIdx] = { mID, msg[mIdx].totPoint + mPoint, mPoint, uIdx, mIdx };
	msgPQ.push({ mID, msg[mIdx].totPoint });
	return users[uIdx].totPoint;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint){
	int uIdx = get_userIndex(mUser);
	int mIdx = get_msgIndex(mID);
	int tIdx = get_msgIndex(mTargetID);
	strcpy(users[uIdx].mUser, mUser);
	users[uIdx].totPoint += mPoint;
	userPQ.push({ mUser, users[uIdx].totPoint });

	msg[mIdx] = { mID, msg[mIdx].totPoint, mPoint, uIdx, msg[tIdx].root };
	msg[tIdx].childList.push_back(mIdx);
	msg[msg[mIdx].root].totPoint += mPoint;
	msgPQ.push({ msg[msg[mIdx].root].mID, msg[msg[mIdx].root].totPoint });
	return msg[msg[mIdx].root].totPoint;
}

void dfs(int cur) {
	visited[cur] = true;
	Message& cur_m = msg[cur];
	cur_m.state = ERASED;
	users[cur_m.user].totPoint -= cur_m.mPoint;
	msg[cur_m.root].totPoint -= cur_m.mPoint;
	userPQ.push({ users[cur_m.user].mUser, users[cur_m.user].totPoint });
	msgPQ.push({ msg[cur_m.root].mID, msg[cur_m.root].totPoint });
	for (int child : cur_m.childList) if (!visited[child] && msg[child].state != ERASED) dfs(child);
}

int erase(int mID){
	for (int i = 0; i < msgCnt; i++) visited[i] = false;
	int mIdx = get_msgIndex(mID);
	dfs(mIdx);
	if (mIdx == msg[mIdx].root) return users[msg[mIdx].user].totPoint;
	else return msg[msg[mIdx].root].totPoint;
}
 
void getBestMessages(int mBestMessageList[]){
	auto& Q = msgPQ;
	int cnt = 0;
	vector<int> popped;
	while (Q.size() && cnt < 5) {
		auto cur = Q.top(); Q.pop();
		int mIdx = get_msgIndex(cur.mID);
		while (Q.size() && cur == Q.top()) { Q.pop(); }
		if (msg[mIdx].root != mIdx) continue;
		if (msg[mIdx].state == ERASED) continue;
		if (msg[mIdx].totPoint != cur.totPoint) continue;
		
		mBestMessageList[cnt++] = cur.mID;
		popped.push_back(mIdx);
	}
	for (int mIdx : popped) Q.push({ msg[mIdx].mID, msg[mIdx].totPoint });
}

void getBestUsers(char mBestUserList[][MAXL + 1]){
	auto& Q = userPQ;
	int cnt = 0;
	vector<int> popped;
	while (Q.size() && cnt < 5) {
		auto cur = Q.top(); Q.pop();
		int uIdx = get_userIndex(cur.mUser);
		while (Q.size() && cur == Q.top()) { Q.pop(); }
		if (users[uIdx].totPoint != cur.totPoint) continue;
		strcpy(mBestUserList[cnt++], cur.mUser);
		popped.push_back(uIdx);
	}
	for (int uIdx : popped) Q.push({ users[uIdx].mUser, users[uIdx].totPoint });
}
#endif
```
