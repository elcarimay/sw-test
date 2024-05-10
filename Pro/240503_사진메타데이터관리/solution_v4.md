```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <vector>
#include <string.h>
#include <cstring>
#include <algorithm>
#include <set>
#include <unordered_map>
#include <queue>
using namespace std;

struct Info {
	int id, date, time;
	vector<const char*> l, p;
	bool operator<(const Info& r)const {
		return (date > r.date) ||
			(date == r.date && (time > r.time));
	}
};

struct Data {
	int id, date, time;
	bool operator<(const Data& r)const {
		return (date > r.date) ||
			(date == r.date && (time > r.time));
	}
};

unordered_map<string, set<Data>> lmap, pmap;
priority_queue<Info> ss;
Info ret;
void input_fun(char c[], priority_queue<Info>& pq) {
	ret = {};
	char id[10], dt[21], l[11], p[55], y[5], month[3], d[3], h[3], minute[3], s[3];
	char* start, * start1, * end, * end1;

	// ID
	start = strchr(c, '['),	end = strchr(start + 1, ']');
	strncpy_s(id, start + 1, end - start - 1); ret.id = atoi(id);

	// year
	start = strchr(end + 1, '['), end = strchr(start + 1, ']');
	strncpy_s(dt, start + 1, end - start - 1); ret.date = atoi(dt);
	strncpy_s(y, dt, 4);
	// month
	start1 = strchr(dt, '/'), end1 = strchr(start1 + 1, '/');
	strncpy_s(month, start1 + 1, end1 - start1 - 1);
	// day
	start1 = strchr(end1, '/'), end1 = strchr(start1 + 1, ',');
	strncpy_s(d, start1 + 1, end1 - start1 - 1);
	// hour
	start1 = end1, end1 = strchr(start1 + 1, ':');
	strncpy_s(h, start1 + 1, end1 - start1 - 1);
	// minute
	start1 = end1, end1 = strchr(start1 + 1, ':');
	strncpy_s(minute, start1 + 1, end1 - start1 - 1);
	// sec
	start1 = end1; strncpy_s(s, start1 + 1, strlen(start1));

	ret.date = atoi(y) * 10'000 + atoi(month) * 100 + atoi(d);
	ret.time = atoi(h) * 10'000 + atoi(minute) * 100 + atoi(s);

	// Loc
	start = strchr(end + 1, '['), end = strchr(start + 1, ']');
	strncpy_s(l, start + 1, end - start - 1); ret.l.push_back(l);

	// People
	start = strchr(end + 1, '['), end = strchr(start + 1, ']');
	strncpy_s(p, start + 1, end - start - 1);
	char temp[11];
	
	start = strchr(p, p[0]);
	while (1) {
		temp[0] = '\0';
		end = strchr(start + 1, ',');
		if (end != NULL) end++;
		strncpy_s(temp, start, end - start-1);
		ret.p.push_back(temp);
		if (end == NULL) break;
		start = end;
	}

	/*char* ptr = strtok(p, ",");
	while (ptr != NULL) {
		temp[0] = '\0';
		strcpy(temp, ptr);
		ret.p.push_back(temp);
		ptr = strtok(NULL, ",");
	}*/
	pq.push(ret);
}

void init(int N, char pictureList[][200]) {
	ss = {}, lmap.clear(), pmap.clear(); 
	for (int i = 0; i < N; i++) input_fun(pictureList[i], ss);
}

void savePictures(int M, char pictureList[][200]) {
	for (int i = 0; i < M; i++) input_fun(pictureList[i], ss);
}

int filterPictures(char mFilter[], int K) {
	string temp = mFilter;
	int s = temp.find('['), e = temp.find(']') - s - 1;
	temp.erase(0, s + 1); temp.erase(e, 1);
	bool type = false; // true: LOC, false: PEOPLE
	if (mFilter[0] == 'L') {
		auto it = lmap[temp].begin();
		advance(it, K - 1);
		return it->id;
	}
	else {
		auto it = pmap[temp].begin();
		advance(it, K - 1);
		return it->id;
	}
}

int deleteOldest(void) {
	auto it = ss.top(); ss.pop();
	int ret = it.id;
	for (auto m : it.l) {
		auto it1 = lmap[m].rbegin();
		lmap[m].erase(*it1);
	}
	for (auto m : it.p) {
		auto it1 = pmap[m].rbegin();
		pmap[m].erase(*it1);
	}
	return ret;
}
#endif // 0

```
