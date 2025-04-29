```cpp
#if 1 // 61 ms
// vector version
// 개별로 group을 설정하고 그룹간 project는 따로 pcnt[a][b]로 관리함
// 프로젝트를 하고 있는지 여부를 pcnt으로 알아보고 반환
// A회사 숫자 + B회사 숫자 = N
#define MAXN 500
#define MAXL 11
#define LM 1003

#include <unordered_map>
#include <vector>
#include <string>
#include <string.h>
using namespace std;

unordered_map<string, int> idMap;
int N, p[LM], pcnt[LM][LM];
vector<int> allyList[LM];
void init(int mNumA, char mCompanyListA[MAXN][MAXL], int mNumB, char mCompanyListB[MAXN][MAXL]){
	idMap.clear(), memset(pcnt, 0, sizeof(pcnt)), N = mNumA + mNumB;
	for (int i = 0; i < N; i++) allyList[i] = { i };
	for (int i = 0; i < mNumA; i++) idMap[mCompanyListA[i]] = p[i] = i;
	for (int i = 0; i < mNumB; i++) idMap[mCompanyListB[i]] = p[mNumA + i] = mNumA + i;
}


void startProject(char mCompanyA[MAXL], char mCompanyB[MAXL]){
	int a = p[idMap[mCompanyA]], b = p[idMap[mCompanyB]];
	pcnt[a][b]++, pcnt[b][a]++;
}

void finishProject(char mCompanyA[MAXL], char mCompanyB[MAXL]){
	int a = p[idMap[mCompanyA]], b = p[idMap[mCompanyB]];
	pcnt[a][b]--, pcnt[b][a]--;
}

void ally(char mCompany1[MAXL], char mCompany2[MAXL]){
	int a = p[idMap[mCompany1]], b = p[idMap[mCompany2]];
	if (a == b) return;
	if (allyList[a].size() < allyList[b].size()) swap(a, b);
	for (int x : allyList[b]) {
		p[x] = a;
		allyList[a].push_back(x);
	}
	for (int i = 0; i < N; i++) {
		pcnt[a][i] += pcnt[b][i];
		pcnt[i][a] += pcnt[i][b];
		pcnt[b][i] = pcnt[i][b] = 0;
	}
}

int conflict(char mCompany1[MAXL], char mCompany2[MAXL]){
	int a = p[idMap[mCompany1]], b = p[idMap[mCompany2]];
	int ret = 0;
	for (int i = 0; i < N; i++)
		if (pcnt[a][i] && pcnt[b][i]) ret += allyList[i].size();
	return ret;
}
#endif // 1 // 61 ms
```
