```cpp
#define _CRT_SECURE_NO_WARNINGS
#define MAXL 10
#include <vector>
#include <queue>
#include <unordered_map>
#include <cstring>
using namespace std;

#define MAX_USERS 10000 // 각 테스트 케이스에서 사용자의 수는 10,000 이하이다.
#define MAX_MESSAGES 50000 // 각 테스트 케이스에서 모든 함수의 호출 횟수는 50,000 이하이다.
#define ERASED 1

struct User
{
	char mUser[MAXL + 1]; // char의 경우 "abc" → "a, b, c, \n"와 같이 끝에 null문자가 하나 더 들어감
	int totPoint;
	User() {
		strcpy(this->mUser, "");
		this->totPoint = 0;
	}
	User(const char mUser[], int totPoint) {
		strcpy(this->mUser, mUser);
		this->totPoint = totPoint;
	}
	bool operator<(const User& user)const {
		return (totPoint < user.totPoint) ||
			(totPoint == user.totPoint && strcmp(mUser, user.mUser) > 0);
			// 문자열을 비교할때 아스키코드로 비교하기 때문에 사전순서로 앞이면 음수, 뒤면 양수
			// 먼저 적어준 문자열(str1)이 뒤에 적어준 문자열(str2)보다 사전 순서로 앞이면 음수를 뒤면 양수를 반환한다.
	}
	bool operator==(const User& user)const {
		return (totPoint == user.totPoint && strcmp(mUser, user.mUser) == 0);
	}
}users[MAX_USERS];
int userCnt;
unordered_map<string, int> userMap;

struct Message
{
	int mID, totPoint, mPoint, user, root, state;
	vector<int> childList;
}msg[MAX_MESSAGES];
int msgCnt;
unordered_map<int, int> msgMap;
bool visited[MAX_MESSAGES];

struct MessageData
{
	int mID, totPoint;
	bool operator<(const MessageData& msg)const {
		return (totPoint < msg.totPoint) ||
			(totPoint == msg.totPoint && mID > msg.mID);
	}
	bool operator==(const MessageData& msg)const {
		return totPoint == msg.totPoint && mID == msg.mID;
	}
};

priority_queue<User> userPQ;
priority_queue<MessageData> msgPQ;

int get_userIdx(string mUser) {
	int uIdx = -1;
	auto cur = userMap.find(mUser); // unordered_map은 find로 못찾으면 end()를 반환함
	if (cur == userMap.end()) userMap.insert({ mUser, uIdx = userCnt++ });
	else uIdx = cur->second;
	return uIdx;
}

int get_msgIdx(int mID) {
	int mIdx = -1;
	auto cur = msgMap.find(mID);
	if (cur == msgMap.end()) msgMap.insert({ mID, mIdx = msgCnt++ });
	else mIdx = cur->second;
	return mIdx;
}

void init() {
	for (int i = 0; i < MAX_USERS; i++) users[i] = {};
	userCnt = 0; userMap.clear();
	for (int i = 0; i < MAX_MESSAGES; i++) msg[i] = {};
	msgCnt = 0; msgMap.clear();
	while (!userPQ.empty()) userPQ.pop();
	while (!msgPQ.empty()) msgPQ.pop();
}

int writeMessage(char mUser[], int mID, int mPoint) {
	int uIdx = get_userIdx(mUser);
	int mIdx = get_msgIdx(mID);
	strcpy(users[uIdx].mUser, mUser);
	users[uIdx].totPoint += mPoint;
	userPQ.push({ mUser, users[uIdx].totPoint });

	msg[mIdx] = { mID, msg[mIdx].totPoint + mPoint, mPoint, uIdx, mIdx };
	msgPQ.push({ mID, msg[mIdx].totPoint });
	return users[uIdx].totPoint;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint) {
	int uIdx = get_userIdx(mUser);
	int mIdx = get_msgIdx(mID);
	int tIdx = get_msgIdx(mTargetID);
	strcpy(users[uIdx].mUser, mUser);
	users[uIdx].totPoint += mPoint;
	userPQ.push({ mUser, users[uIdx].totPoint });

	msg[mIdx] = { mID, msg[mIdx].totPoint + mPoint, mPoint, uIdx, msg[tIdx].root };
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
	for (int child : cur_m.childList) if (!visited[child] && msg[child].state != ERASED)
		dfs(child);
}

int erase(int mID) {
	for (int i = 0; i < msgCnt; i++) visited[i] = false;
	int mIdx = get_msgIdx(mID);
	dfs(mIdx);
	if (mIdx == msg[mIdx].root) return users[msg[mIdx].user].totPoint;
	else return msg[msg[mIdx].root].totPoint;
}

void getBestMessages(int mBestMessageList[]) {
	auto& Q = msgPQ;
	int cnt = 0;
	vector<int> popped;
	while (!Q.empty() && cnt < 5) {
		auto cur = Q.top(); Q.pop();
		int mIdx = get_msgIdx(cur.mID);
		while (!Q.empty() && cur == Q.top()) Q.pop();
		if (msg[mIdx].root != mIdx) continue;
		if (msg[mIdx].state == ERASED) continue;
		if (msg[mIdx].totPoint != cur.totPoint) continue;
		mBestMessageList[cnt++] = cur.mID;
		popped.push_back(mIdx);
	}
	for (int mIdx : popped) Q.push({ msg[mIdx].mID, msg[mIdx].totPoint });
}

void getBestUsers(char mBestUserList[][MAXL + 1]) {
	auto& Q = userPQ;
	int cnt = 0;
	vector<int> popped;
	while (!Q.empty() && cnt < 5) {
		auto cur = Q.top(); Q.pop();
		int uIdx = get_userIdx(cur.mUser);
		while (!Q.empty() && cur == Q.top()) Q.pop();
		if (users[uIdx].totPoint != cur.totPoint) continue;
		strcpy(mBestUserList[cnt++], cur.mUser);
		popped.push_back(uIdx);
	}
	for (int uIdx : popped) Q.push({ users[uIdx].mUser, users[uIdx].totPoint });
}
```
