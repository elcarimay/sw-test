```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <set>
#include <unordered_map>
#include <string>
using namespace std;

#define MAXM (10)
#define MAXL (11)

struct Info{
	int start, end;
	char title[MAXL];
};

Info info[10003];
vector<int> member[20003]; // memberid, meetingid
unordered_map<string, int> memberMap;
int memberCnt;

vector<int> meeting[10003]; // meetingid, memberid
unordered_map<string, int> meetingMap;
int meetingCnt;

void init() {
	for (int i = 0; i < 20000; i++) member[i].clear();
	for (int i = 0; i < 10000; i++) meeting[i].clear();
	memberMap.clear(); memberCnt = 0; meetingMap.clear(); meetingCnt = 0;
}

int getId(char name[MAXL]) {
	int id;	auto it = memberMap.find(name);
	if (it == memberMap.end()) {id = memberCnt; memberMap[name] = memberCnt++;}
	else id = memberMap[name];
	return id;
}

bool overlab(int meetingid, int memberid, int start, int end) {
	for (auto meetid : member[memberid]) {
		if (meetid == meetingid) continue;
		if (!(end < info[meetid].start || info[meetid].end < start)) return true;
	}
	return false;
}

int addMeeting(char mMeeting[MAXL], int M, char mMemberList[MAXM][MAXL], int mStartTime, int mEndTime) {
	int meetingid = meetingCnt++;
	meetingMap[mMeeting] = meetingid;
	for (int i = 0; i < M; i++) {
		int memberid = getId(mMemberList[i]);
		if (!overlab(meetingid, memberid, mStartTime, mEndTime)) {
			member[memberid].push_back(meetingid);
			meeting[meetingid].push_back(memberid);
			info[meetingid] = { mStartTime, mEndTime};
			strcpy(info[meetingid].title, mMeeting);
		}
	}
	return meeting[meetingid].size();
}

int cancelMeeting(char mMeeting[MAXL]) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return 0;
	int meetingid = it->second;
	if (meeting[meetingid].empty()) return 0;
	for (int memberid : meeting[meetingid])
		for (int i = 0; i < member[memberid].size();i++)
			if (member[memberid][i] == meetingid) member[memberid].erase(member[memberid].begin() + i);
	meeting[meetingid].clear();
	meetingMap.erase(it);
	return 1;
}

int changeMeetingMember(char mMeeting[MAXL], char mMember[MAXL]) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return -1;
	int meetingid = it->second;
	int memberid = getId(mMember);
	if (meeting[meetingid].empty()) return -1;
	for (int i = 0; i < meeting[meetingid].size(); i++)
		if (meeting[meetingid][i] == memberid) {
			meeting[meetingid].erase(meeting[meetingid].begin() + i);
			for (int j = 0; j < member[memberid].size(); j++)
				if (member[memberid][j] == meetingid) member[memberid].erase(member[memberid].begin() + j);
			return 0;
		}
	if (!overlab(meetingid, memberid, info[meetingid].start, info[meetingid].end)) {
		member[memberid].push_back(meetingid);
		meeting[meetingid].push_back(memberid);
		return 1;
	}
	else return 2;
}

int changeMeeting(char mMeeting[MAXL], int mStartTime, int mEndTime) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return 0;
	int meetingid = it->second;
	info[meetingid].start = mStartTime; info[meetingid].end= mEndTime;
	for (int i = 0; i < meeting[meetingid].size(); i++) {
		int memberid = meeting[meetingid][i];
		if (overlab(meetingid, memberid, mStartTime, mEndTime)) {
			meeting[meetingid].erase(meeting[meetingid].begin() + i); i--;
			for (int j = 0; j < member[memberid].size(); j++)
				if (member[memberid][j] == meetingid) {
					member[memberid].erase(member[memberid].begin() + j); break;
				}
		}
	}
	if (meeting[meetingid].size() == 0) meetingMap.erase(it);
	return meeting[meetingid].size();
}

void checkNextMeeting(char mMember[MAXL], int mTime, char mResult[MAXL]) {
	mResult[0] = 0;
	auto it1 = memberMap.find(mMember);
	if (it1 == memberMap.end()) return;
	int minTime = 1000;
	int memberid = memberMap[mMember];
	for (auto meetid : member[memberid]) {
		if(mTime < info[meetid].start)
			if (info[meetid].start < minTime) {
				minTime = info[meetid].start;
				strcpy(mResult, info[meetid].title);
			}
	}
}
```
