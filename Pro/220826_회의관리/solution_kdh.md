```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <set>
#include <unordered_map>
#include <string>
using namespace std;

#define MAXM (10)
#define MAXL (11)

struct Time {
	int end, start;
	string title;
	bool operator<(const Time& r)const {
		if (start != r.start) return start < r.start;
		return end < r.end;
	}
};

set<Time> info[10003]; // meetingid, (endTime, startTime, title)

vector<int> member[20003]; // memberid, meetingid
unordered_map<string, int> memberMap;
int memberCnt;

vector<int> meeting[10003]; // meetingid, memberid
unordered_map<string, int> meetingMap;
int meetingCnt;

void init() {
	for (int i = 0; i < 20000; i++) member[i].clear();
	for (int i = 0; i < 10000; i++) {
		meeting[i].clear(); info[i].clear();
	}
	memberMap.clear(); memberCnt = 0; meetingMap.clear(); meetingCnt = 0;
}

int getId(unordered_map<string,int>& m, char name[MAXL], int& cnt) {
	int id;
	auto it = m.find(name);
	if (it == m.end()) {
		id = cnt;
		m[name] = cnt++;
	}
	else id = m[name];
	return id;
}

int addMeeting(char mMeeting[MAXL], int M, char mMemberList[MAXM][MAXL], int mStartTime, int mEndTime) {
	int meetingid = meetingCnt++;
	meetingMap[mMeeting] = meetingid;
	for (int i = 0; i < M; i++) {
		int mid = getId(memberMap, mMemberList[i], memberCnt);
		bool flag = true;
		if (member[mid].size()) {
			for (auto time : member[mid])
				if (!(mEndTime<time.start || time.end<mStartTime)) flag = false;
		}
		if(flag) {
			member[mid].insert({ mEndTime, mStartTime, meetingid });
			meeting[meetingid].push_back(mid);
		}
	}
	return meeting[meetingid].size();
}

int cancelMeeting(char mMeeting[MAXL]) {
	if (meetingMap.find(mMeeting) == meetingMap.end()) return 0;
	int meetingid = getId(meetingMap, mMeeting, meetingCnt);
	for (int i = 0; i < meeting[meetingid].size(); i++) {
		int memberid = meeting[meetingid][i];
		auto it = member[memberid].begin();
		while (it != member[memberid].end()) {
			if (it->meetingid == meetingid) {
				member[memberid].erase(it--);
				meeting[meetingid].erase(meeting[meetingid].begin() + i);
				i--; break;
			}
			it++;
		}
	}
	return 1;
}

int changeMeetingMember(char mMeeting[MAXL], char mMember[MAXL]) {
	auto it = meetingMap.find(mMeeting);
	if (meetingMap.find(mMeeting) == meetingMap.end()) return -1;
	int meetingid = it->second;
	int memberid = getId(memberMap, mMember, memberCnt);
	for (int i = 0; i < meeting[meetingid].size(); i++) {
		if (meeting[meetingid][i] == memberid) {
			meeting[meetingid].erase(meeting[meetingid].begin() + i);
			for (auto nx : member[memberid]) {
				if (nx.meetingid == meetingid) {
					member[memberid].erase(member[memberid].find(nx));
					if (meeting[meetingid].empty()) meetingMap.erase(it);
					return 0;
				}
			}
		}
	}
	for (auto mid : meeting[meetingid]) {
		for (auto time : member[memberid]) {
			for (auto time2 : member[mid]) {
				if (!(time.end < time2.start || time2.end < time.start))
					return 2;
			}
		}
	}
	meeting[meetingid].push_back(memberid);
	return 1;
}

int changeMeeting(char mMeeting[MAXL], int mStartTime, int mEndTime){
	auto it = meetingMap.find(mMeeting);
	if (meetingMap.find(mMeeting) == meetingMap.end()) return 0;
	int meetingid = it->second;
	for (int i = 0; i < meeting[meetingid].size(); i++) {
		auto it1 = member[i].begin();
		bool flag = true;
		while (it1 != member[i].end()) {
			if (it1->meetingid == meetingid) {
				member[i].erase(it1);
				meeting[meetingid].erase(meeting[meetingid].begin() + i);
				break;
			}
			if (!(mEndTime < it1->start || it1->end < mStartTime)) {
				flag = false;
			}
			it1++;
		}
		if (!flag) meeting[meetingid].erase(meeting[meetingid].begin() + i);
	}
	return meeting[meetingid].size();
}

void checkNextMeeting(char mMember[MAXL], int mTime, char mResult[MAXL]){
	int mid = getId(memberMap, mMember, memberCnt);
	auto it = member[mid].upper_bound({mTime});
	if (it == member[mid].end()) { mResult[0] = '\0'; return; }
	it++;
	if (it == member[mid].end()) { mResult[0] = '\0'; return; }
	strcpy(mResult, meetingMap_reverse[it->meetingid].c_str());
}
```
