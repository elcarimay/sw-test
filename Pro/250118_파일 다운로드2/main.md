```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
#include <stdio.h>
#define MAX_COM			1000
#define MAX_ONEFILE		50
extern void init(int N, int mFileCnt[], int mFileID[][MAX_ONEFILE], int mFileSize[][MAX_ONEFILE]);
extern void makeNet(int K, int mID[], int mComA[], int mComB[], int mDis[]);
extern void removeLink(int mTime, int mID);
extern int downloadFile(int mTime, int mComA, int mFileID);
extern int getFileSize(int mTime, int mComA, int mFileID);
#define CMD_INIT		0
#define CMD_MAKENET		1
#define CMD_REMOVELINK	2
#define CMD_DOWNLOAD	3
#define CMD_GETSIZE		4
static int sFileid[500], sFilesize[500];
static int fileCnt[MAX_COM], fileID[MAX_COM][MAX_ONEFILE], fileSize[MAX_COM][MAX_ONEFILE];
static int linkID[MAX_COM], linkA[MAX_COM], linkB[MAX_COM], linkDis[MAX_COM];
static bool run(){
	int Q, N, K, time, id, comA;
	int ret, ans;
	bool ok = false;
	scanf("%d", &Q);
	for (int q = 0; q < Q; q++){
		int cmd;
		scanf("%d", &cmd);
		if (cmd == CMD_INIT){
			scanf("%d %d", &N, &K);
			for (int i = 0; i < K; i++)
				scanf("%d %d", &sFileid[i], &sFilesize[i]);
			for (int i = 0; i < N; i++) {
				scanf("%d", &fileCnt[i]);
				for (int k = 0; k < fileCnt[i]; k++) {
					scanf("%d", &id);
					fileID[i][k] = sFileid[id];
					fileSize[i][k] = sFilesize[id];
				}
			}
			init(N, fileCnt, fileID, fileSize);
			ok = true;
		}
		else if (cmd == CMD_MAKENET){
			scanf("%d", &K);
			for (int i = 0; i < K; i++)
				scanf("%d %d %d %d", &linkID[i], &linkA[i], &linkB[i], &linkDis[i]);
			makeNet(K, linkID, linkA, linkB, linkDis);
		}
		else if (cmd == CMD_REMOVELINK){
			scanf("%d %d", &time, &id);
			removeLink(time, id);
		}
		else if (cmd == CMD_DOWNLOAD){
			scanf("%d %d %d", &time, &comA, &id);
			ret = downloadFile(time, comA, id);
			scanf("%d", &ans);
			if (ans != ret) ok = false;
		}
		else if (cmd == CMD_GETSIZE){
			scanf("%d %d %d", &time, &comA, &id);
			ret = getFileSize(time, comA, id);
			scanf("%d", &ans);
			if (ans != ret) ok = false;
		}
		else ok = false;
	}
	return ok;
}
#include <time.h>
int main(){
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);
	int T, MARK;
	scanf("%d %d", &T, &MARK);
	for (int tc = 1; tc <= T; tc++){
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	printf("Performance: %d ms\n", clock() - start);
	return 0;
}
```
