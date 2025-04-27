```cpp
// union find version
// 그룹당 회사 숫자만 관리 → cnt[1003]
// pcnt[gid1][gid2] = gid1그룹과 gid2그룹의 협업 중인 project 수
// A 그룹은 numA까지, B 그룹은 numA + numB까지 관리
// union할 때 그룹당 숫자를 더해줌
#include<unordered_map>
#include<unordered_set>
#include<string>
#include<vector>
#include<algorithm>
#include<string.h>
using namespace std;

#define MAXN 1003

unordered_map<string, int> idMap;
int N, p[MAXN], r[MAXN], cnt[MAXN], pcnt[MAXN][MAXN];
#define MAXN   (500)
#define MAXL   (11)

void init(int mNumA, char mCompanyListA[MAXN][MAXL], int mNumB, char mCompanyListB[MAXN][MAXL]) {
	idMap.clear(), memset(pcnt, 0, sizeof(pcnt));
	N = mNumA + mNumB;
	for (int i = 0; i < N; i++) p[i] = i, r[i] = 0, cnt[i] = 1;
	for (int i = 0; i < mNumA; i++) idMap[mCompanyListA[i]] = i;
	for (int i = 0; i < mNumB; i++) idMap[mCompanyListB[i]] = mNumA + i;
}

int find(int x) {
	if (p[x] == x) return x;
	return p[x] = find(p[x]);
}

void startProject(char mCompanyA[MAXL], char mCompanyB[MAXL]) {
	int a = find(idMap[mCompanyA]), b = find(idMap[mCompanyB]);   // a,b:그룹번호
	pcnt[a][b]++, pcnt[b][a]++;
}

void finishProject(char mCompanyA[MAXL], char mCompanyB[MAXL]) {
	int a = find(idMap[mCompanyA]), b = find(idMap[mCompanyB]);   // a,b:그룹번호
	pcnt[a][b]--, pcnt[b][a]--;
}

void ally(char mCompany1[MAXL], char mCompany2[MAXL]) {
	int a = find(idMap[mCompany1]), b = find(idMap[mCompany2]);
	if (a == b) return;
	if (r[a] < r[b]) swap(a, b);
	p[b] = a;
	cnt[a] += cnt[b];
	if (r[a] == r[b]) r[a]++;
	for (int i = 0; i < N; i++) {
		pcnt[a][i] += pcnt[b][i];
		pcnt[i][a] += pcnt[i][b];
		pcnt[i][b] = pcnt[b][i] = 0;
	}
}


int conflict(char mCompany1[MAXL], char mCompany2[MAXL]) {
	int a = find(idMap[mCompany1]), b = find(idMap[mCompany2]);
	int ret = 0;
	for (int i = 0; i < N; i++)
		if (pcnt[a][i] && pcnt[b][i]) ret += cnt[i];
	return ret;
}
```
