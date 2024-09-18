```cpp
// 따로 대기열 배열을 만들지 않고 valid로 체크
// 3자리까지 hash로 등록하고 이후 3자리를 검색해서 같은지 확인하여 처리
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <string.h>
#include <string>
#include <unordered_map>
using namespace std;


unordered_map<string, int> hmap; // hmap[str] = id(0~) 순서대로 번호 부여
vector<int> V[26 * 26 * 26]; // V[hash] = {id,...} 앞 세자기라 같은 값끼리 분류
char S[50003][10]; // S[id] = str
bool valid[50003]; // valid[id] = 1/0
int head, tail;

void init(){
	head = tail = 0;
	for (auto& v : V) v.clear();
	memset(valid, 0, sizeof(valid));
	hmap.clear();
}

int gethash(char* s) {
	int hash = 0;
	for (int i = 0;i < 3;i++) hash = hash * 26 + (s[i] - 'a');
	return hash;
}

void loginID(char mID[10]){
	if (hmap.count(mID)) valid[hmap[mID]] = 0;
	int id = hmap[mID] = tail++;
	valid[id] = 1;
	strcpy(S[id], mID);
	V[gethash(mID)].push_back(id);
}

int closeIDs(char mStr[10]){
	int ret = 0;
	int len = strlen(mStr);
	auto& v = V[gethash(mStr)]; // id vector
	for (int i = 0;i < v.size();) {
		int id = v[i];
		if (strncmp(S[id] + 3, mStr + 3, len - 3)) { // prefix로 시작하지 않는 경우
			i++; continue;
		}
		if (valid[id]) { // prefix로 시작하는데 유효한 id인 경우
			ret++; valid[id] = 0; hmap.erase(S[id]);
		}
		v[i] = v.back(); // 유효하든 유효하지 않든 hash table에서 삭제
		v.pop_back();
	}
	return ret;
}

void connectCnt(int mCnt){
	while (mCnt) {
		int id = head++; // 먼저 들어온 순서대로 대기열 에서 삭제
		if (valid[id]) { // 유효한 경우
			valid[id] = 0;
			hmap.erase(S[id]);
			mCnt--;
		}
	}
}

int waitOrder(char mID[10]){
	if (!hmap.count(mID)) return 0;
	int destID = hmap[mID];
	int ret = 0;
	for (int id = head; id <= destID;id++)
		if (valid[id]) ret++;
	return ret;
}
```
