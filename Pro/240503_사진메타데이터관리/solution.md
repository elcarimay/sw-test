```cpp
#if 1 // 230 ms
/*
1. Chained Hash Table 생성
unordered_map<string, int> map;
map[KEY] = value;

unordered_map<string, vector<int>> map;
vector<int>& vec = map[key];
vec.push_back(value);

map[key].push_back(value);

2. 정렬을 위한 Time 값 처리
year  1x12x31x24x60x60	= 32140800
month 1x31x24x60x60		= 2678400
day   1x24x60x60		= 86400
hh	  1x6x60			= 3600
mm	  1x60				= 60
ss	  1					= 1

위의값을 다 더해서 사용, 자료형은 일단 unsigned long long으로 사용.
= > data와 time으로 분기해서 사용하면 if문 2개를 사용해야 하기때문에 느려짐.

3. 문자열 파싱
#include <stdlib.h>
strtok함수 사용 - delimeter를 제외한 문자열을 토큰으로 반환해줌
char delim[] = ":[]/,";
ID: [384748] , TIME : [2015 / 2 / 2, 0:21 : 50] , LOC : [school] , PEOPLE : [I, dad, mom]
char* p = strtok(pictureList, delim); ID // 토큰을 하나씩 줌.
char* p = strtok(nullptr, delim); 384778
char* p = strtok(nullptr, delim); TIME
char* p = strtok(nullptr, delim); 2015
// 내부 정적 포인터에 전달된 문자 배열주소 저장
// 다음 문자열 주소 반환

- 새로운 데이터는 이전보다 최신 데이터가 들어온다.
=> 새로운 데이터만 정렬해서 추가해준다.
=> filterPicture 함수에서 정렬하면 안된다.

set을 쓰면 안될 것 같구요.
vector 에 미리 정렬 시켜 놓고, 이진 탐색으로 찾으면 시간이 1/10 이하로 확줄 것 같네요.

map 정렬하는 stl인데 key값을 기준으로 정렬
*/
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <string>
#include <unordered_map>
#include <stdlib.h>
#include <algorithm>
using namespace std;

#define ull unsigned long long
#define MAXM 110'100

struct Meta
{
	int id;
	ull time;
	string loc;
	vector<string> people;
	bool operator<(const Meta& r)const {
		return time < r.time;
	}
}metas[MAXM];
int ms, me;

unordered_map<string, vector<int>> mapLoc, mapPeo;

char delim[] = ":[]/,";

void parsing(char pictureList[]) {
	char* p = strtok(pictureList, delim);
	while (p) {
		if (p[0] == 'I') {
			p = strtok(nullptr, delim);
			metas[me].id = atoi(p);
		}
		else if (p[0] == 'T') {
			int year, month, day, hh, mm, ss;
			p = strtok(nullptr, delim); year = atoi(p);
			p = strtok(nullptr, delim); month = atoi(p);
			p = strtok(nullptr, delim); day = atoi(p);
			p = strtok(nullptr, delim); hh = atoi(p);
			p = strtok(nullptr, delim); mm = atoi(p);
			p = strtok(nullptr, delim); ss = atoi(p);
			Meta& meta = metas[me];
			meta.time = year; meta.time *= 12;
			meta.time += month; meta.time *= 31;
			meta.time += day; meta.time *= 24;
			meta.time += hh; meta.time *= 60;
			meta.time += mm; meta.time *= 60;
			meta.time += ss;
		}
		else if (p[0] == 'L') {
			p = strtok(nullptr, delim);
			metas[me].loc = p;
		}
		else if (p[0] == 'P') {
			metas[me].people.clear();
			while (p = strtok(nullptr, delim)) {
				metas[me].people.push_back(p);
			}
		}
		p = strtok(nullptr, delim);
	}
	++me;
}

void init(int N, char pictureList[][200]) {
	// init
	ms = me = 0;
	mapLoc.clear(), mapPeo.clear();

	// parsing
	for (int i = 0; i < N; i++)
		parsing(pictureList[i]);

	// sort
	sort(metas, metas + me);

	// map
	for (int i = 0; i < N; i++)
	{
		mapLoc[metas[i].loc].push_back(i);
		for (auto& p : metas[i].people) {
			mapPeo[p].push_back(i);
		}
	}
}

void savePictures(int M, char pictureList[][200]) {
	int pos = me;
	for (int i = 0; i < M; i++)
		parsing(pictureList[i]);

	sort(metas + pos, metas + me); // 새로 들어오는 data는 최신이기 때문

	for (int i = pos; i < me; i++)
	{
		mapLoc[metas[i].loc].push_back(i);
		for (auto& p : metas[i].people) {
			mapPeo[p].push_back(i);
		}
	}
}

int filterPictures(char mFilter[], int K) {
	char* p = strtok(mFilter, delim);
	char type = p[0];
	p = strtok(nullptr, delim);
	auto& vec = (type == 'L') ? mapLoc[p] : mapPeo[p];
	return metas[vec[vec.size() - K]].id;
}

int deleteOldest(void) {
	return metas[ms++].id;
}

#endif // 1

```
