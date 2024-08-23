```cpp
#if 1 // ms
#define _CRT_SECURE_NO_WARNINGS
#include<string.h>
#include<vector>
#include<unordered_map>
using namespace std;
using pii = pair<int, int>; // id, start

#define MAXL 7
#define MAXM 100

struct Result {
	char mTitle[MAXL];
	int  mScore;
};

int cnt;

struct Track {
	char title[10];
	int size, org_scale[103], scale[103], delta[103], tempo[103];
}track[403];

unordered_map<int, int> idMap;
unordered_map<int, vector<pii>> scaleMap, deltaMap, tempoMap; // tempMap, deltaMap
unordered_map<string, int> titleMap;
int hcnt;

void init() {
	cnt = hcnt = 0; idMap.clear(); scaleMap.clear(); deltaMap.clear(); tempoMap.clear(); titleMap.clear();
}

int getValue(int data[], int minus = 0) { // 주어진 배열의 8개를 하나로 묶는다 data[0~7]의 값을 정수로 변환
	int x = 0; // delta의 경우 8개값 중 가장 작은 값을 minus로 설정하고 최소값을 0으로 만듬
	for (int i = 0; i < 8; i++)
		x = x * 10 + data[i] - minus;
	return x;
}

int getMin(int data[]) {
	int minV = 10;
	for (int i = 0; i < 8; i++) minV = min(minV, data[i]);
	return minV;
}

void add(char mTitle[MAXL], int mSize, int mScale[MAXM], int mTempo[MAXM]) {
	strcpy(track[cnt].title, mTitle);
	titleMap[mTitle] = cnt;
	track[cnt].size = mSize;
	for (int i = 0; i < mSize; i++) {
		track[cnt].org_scale[i] = mScale[i];
		if (i >= mSize - 7) continue;
		track[cnt].scale[i] = getValue(mScale + i);
		track[cnt].delta[i] = getValue(mScale + i, getMin(mScale + i));
		track[cnt].tempo[i] = getValue(mTempo + i);
		
		scaleMap[track[cnt].scale[i]].push_back({ cnt, i }); // 음정
		deltaMap[track[cnt].delta[i]].push_back({ cnt, i }); // 차이
		tempoMap[track[cnt].tempo[i]].push_back({ cnt, i }); // 박자
	}
	cnt++;
}

void erase(char mTitle[MAXL]) {
	int id = titleMap[mTitle];
	track[id].size = 0;
}

void changePitch(char mTitle[MAXL], int mDelta) {
	int id = titleMap[mTitle];
	for (int i = 0; i < track[id].size; i++) {
		int temp = track[id].org_scale[i] + mDelta;
		if (temp < 0 || temp > 9) return;
	}
	for (int i = 0; i < track[id].size; i++) {
		track[id].org_scale[i] += mDelta;
		if (i < 7) continue;
		scaleMap.erase(scaleMap.find(track[id].scale[i - 7]));
		track[id].scale[i - 7] = getValue(track[id].org_scale + i - 7);
		scaleMap[track[id].scale[i - 7]].push_back({ id, i - 7 });
	}
}

/*ⓐ 음계, 박자 모두가 일치할 경우 100점을 얻는다.
ⓑ 음계는 다르지만 마디 전체의 조성을 변경시켰을 때 음계를 같게 만들 수 있고 이때 박자가 일치하는 경우 50점을 얻는다.
ⓒ 음계가 다르고 마디 전체의 조성을 변경시켜도 음계를 같게 만들 수 없지만 박자가 일치하는 경우 10점을 얻는다.
ⓓ 박자는 다르지만, 음계가 일치하는 경우 5점을 얻는다.
ⓔ 박자, 음계 모두가 다르지만 마디 전체의 조성을 변화시켰을 때 음계를 같게 만들 수 있는 경우 1점을 얻는다.
ⓕ ⓐ 부터 ⓔ까지 경우들 중 어느 것도 해당하지 않는 경우 0점을 얻는다.*/

int cal(int scale, int delta, int tempo) {
	if (scale && delta && tempo) return 100; // a
	if (!scale && delta && tempo) return 50; // b
	if (!scale && !delta && tempo) return 10; // c
	if (scale && delta && !tempo) return 5; // d
	if (!scale && delta && !tempo) return 1; // e
	//if (!scale && !delta && !tempo) return 0; // f
	return 0;
}

Result getSimilarity(int mSize, int mScale[MAXM], int mTempo[MAXM]) {
	Result res = {"", 0};
	int resultScore = -1;
	int score[403] = {};
	bool scale_flag = 0, delta_flag = 0, tempo_flag = 0;
	for (int i = 0; i < mSize - 7; i++) {
		int scale = getValue(mScale + i);
		int delta = getValue(mScale + i, getMin(mScale + i));
		int tempo = getValue(mTempo + i);
		auto cur = deltaMap.find(delta);
		if (cur != deltaMap.end()) {
			delta_flag = 1;
			auto nx = cur->second;
			for (auto nx2 : nx) {
				scale_flag = 0, tempo_flag = 0;
				int cnt = nx2.first, start = nx2.second;
				if (track[cnt].scale[start] == scale) scale_flag = 1;
				if (track[cnt].tempo[start] == tempo) tempo_flag = 1;
				score[cnt] += cal(scale_flag, delta_flag, tempo_flag);
			}
		}
		auto cur2 = tempoMap.find(tempo);
		if (cur2 != tempoMap.end()) {
			tempo_flag = 1;
			auto nx = cur2->second;
			for (auto nx2 : nx) {
				scale_flag = 0, delta_flag = 0;
				int cnt = nx2.first, start = nx2.second;
				if (track[cnt].scale[start] == scale) scale_flag = 1;
				if (track[cnt].delta[start] == delta) delta_flag = 1;
				if(!scale_flag && !delta_flag)
					score[cnt] += cal(scale_flag, delta_flag, tempo_flag);
			}
		}
	};
		
	for (int i = 0; i <= cnt; i++) {
		if (!track[i].size) continue;
		if (score[i] > resultScore) {
			resultScore = score[i];
			strcpy(res.mTitle, track[i].title);
		}
	}
	res.mScore = resultScore;
	return res;
}
#endif // 1 // ms

```
