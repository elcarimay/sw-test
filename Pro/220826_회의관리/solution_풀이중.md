```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <set>
#include <unordered_map>
#include <string>
using namespace std;

#define MAXM (10)
#define MAXL (11)

struct Time{
	int start, end;
	char title[MAXL];
	bool operator<(const Time& r)const {
		if (start != r.start) return start < r.start;
		return end < r.end;
	}
};

set<Time>::iterator iter[10003];
set<Time> time; // meetingid, (endTime, startTime, title)

vector<int> member[20003]; // memberid, meetingid
unordered_map<string, int> memberMap;
int memberCnt;

vector<int> meeting[10003]; // meetingid, memberid
unordered_map<string, int> meetingMap;
int meetingCnt;

void init() {
	for (int i = 0; i < 20000; i++) member[i].clear();
	for (int i = 0; i < 10000; i++) { meeting[i].clear(); iter[i] = {}; }
	time.clear(); memberMap.clear(); memberCnt = 0; meetingMap.clear(); meetingCnt = 0;
}

int getId(char name[MAXL]) {
	int id;	auto it = memberMap.find(name);
	if (it == memberMap.end()) {id = memberCnt; memberMap[name] = memberCnt++;}
	else id = memberMap[name];
	return id;
}

bool overlab(int memberid, int start, int end) {
	for (int i = 0; i < meetingCnt - 1; i++)
		for (int mid : meeting[i]) if (mid == memberid)
			if (!(end < iter[i]->start || iter[i]->end < start)) return true;
	return false;
}

int addMeeting(char mMeeting[MAXL], int M, char mMemberList[MAXM][MAXL], int mStartTime, int mEndTime) {
	int meetingid = meetingCnt++;
	meetingMap[mMeeting] = meetingid;
	for (int i = 0; i < M; i++) {
		Time tmp = { mStartTime, mEndTime };
		strcpy(tmp.title, mMeeting);
		iter[meetingid] = time.insert(tmp).first;
		int memberid = getId(mMemberList[i]);
		if (!overlab(memberid, mStartTime, mEndTime)) {
			member[memberid].push_back(meetingid);
			meeting[meetingid].push_back(memberid);
		}
	}
	return meeting[meetingid].size();
}

int cancelMeeting(char mMeeting[MAXL]) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return 0;
	int meetingid = it->second;
	for (int memberid:meeting[meetingid]) 
		for (int i = 0; i < member[memberid].size();i++)
			if (member[memberid][i] == meetingid) member[memberid].erase(member[memberid].begin() + i);
	meeting[meetingid].clear(); meetingMap.erase(it); time.erase(iter[meetingid]);
	return 1;
}

int changeMeetingMember(char mMeeting[MAXL], char mMember[MAXL]) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return -1;
	int meetingid = it->second;
	int memberid = getId(mMember);
	for (int i = 0; i < meeting[meetingid].size(); i++)
		if (meeting[meetingid][i] == memberid) {
			meeting[meetingid].erase(meeting[meetingid].begin() + i);
			for (int j = 0; j < member[memberid].size(); j++)
				if (member[memberid][j] == meetingid) {
					member[memberid].erase(member[memberid].begin() + j);
				}
			return 0;
		}
	if (!overlab(memberid, iter[meetingid]->start, iter[meetingid]->end)) {
		member[memberid].push_back(meetingid);
		meeting[meetingid].push_back(memberid);
		return 1;
	}
	else return 2;
}

bool overlab_meet(int meetingid, int memberid, int start, int end) {
	for (int i = 0; i < meetingCnt; i++) {
		if (i == meetingid) continue; // 변경하고자하는 회의일때는 continue;
		if (!(end < iter[i]->start || iter[i]->end < start))
			for (auto mid : meeting[i]) if (mid == memberid) return true;
	}
	return false;
}

int changeMeeting(char mMeeting[MAXL], int mStartTime, int mEndTime) {
	auto it = meetingMap.find(mMeeting);
	if (it == meetingMap.end()) return 0;
	int meetingid = it->second;
	time.erase(iter[meetingid]);
	Time tmp = { mStartTime, mEndTime };
	strcpy(tmp.title, mMeeting);
	iter[meetingid] = time.insert(tmp).first;
	for (int i = 0; i < meeting[meetingid].size(); i++) {
		int memberid = meeting[meetingid][i];
		if (overlab_meet(meetingid, memberid, mStartTime, mEndTime)) {
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
	mTime++;
	int memberid = memberMap[mMember];
	while (1) {
		auto it = time.upper_bound({ mTime });
		if (it == time.end()) {
			mResult[0] = '\0'; return;
		}
		int meetingid = meetingMap[it->title];
		for (auto mid : meeting[meetingid])
			if(mid == memberid) strcpy(mResult, it->title); return;
		it++;
		if (it == time.end()) {
			mResult[0] = '\0'; return;
		}
	}
}
```
