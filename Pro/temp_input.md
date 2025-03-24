```cpp
main.cpp
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
	int Q, N, K, time, id, comA, int ret, ans; bool ok = false;
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
					scanf("%d", &id); fileID[i][k] = sFileid[id]; fileSize[i][k] = sFilesize[id];
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
	return 0;
}

solution.cpp
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <queue>
#include <algorithm>
#include <cstring>
using namespace std;

const int MAX_N = 1001;
const int MAX_FILE = 501;
const int MAX_LINK = 50001;
const int MAX_DOWNLOAD_PATH = 5;
const int DOWNLOAD_RATE = 9;
int currentTime = 0;
struct Link {
    int id, comA, comB, distance, removeTime;
};

struct File {
    int id, size, downloadedSize, downloadTime;
};
vector<Link> links;
vector<int> linksByComputer[MAX_N];
unordered_map<int, int> linkIdToIndex;
unordered_map<int, File> computerFiles[MAX_N];
unordered_map<int, vector<pair<int, int>>> fileLocations;
int N;
vector<vector<int>> findDownloadPaths(int fromCom, int targetCom) {
    vector<vector<int>> paths;
    queue<vector<int>> pathQueue;
    vector<bool> visited(N + 1, false);
    pathQueue.push({ fromCom });
    visited[fromCom] = true;
    while (!pathQueue.empty() && paths.size() < MAX_DOWNLOAD_PATH) {
        auto currentPath = pathQueue.front(); pathQueue.pop();
        int lastCom = currentPath.back();
        if (lastCom == targetCom) {
            int totalDistance = 0;
            bool validPath = true;
            for (size_t i = 0; i + 1 < currentPath.size(); ++i) {
                bool foundLink = false;
                for (int linkId : linksByComputer[currentPath[i]]) {
                    Link& link = links[linkIdToIndex[linkId]];
                    if ((link.comA == currentPath[i] && link.comB == currentPath[i + 1]) ||
                        (link.comB == currentPath[i] && link.comA == currentPath[i + 1])) {
                        if (link.removeTime > 0 && link.removeTime <= currentTime) {
                            validPath = false; break;
                        }
                        totalDistance += link.distance;
                        foundLink = true;
                        break;
                    }
                }
                if (!foundLink || !validPath) {
                    validPath = false; break;
                }
            }
            if (validPath && totalDistance <= 5) paths.push_back(currentPath);
            continue;
        }
        if (currentPath.size() > 6) continue;
        for (int linkId : linksByComputer[lastCom]) {
            Link& link = links[linkIdToIndex[linkId]];
            if (link.removeTime > 0 && link.removeTime <= currentTime) continue;
            int nextCom = (link.comA == lastCom) ? link.comB : link.comA;
            if (!visited[nextCom]) {
                auto newPath = currentPath;
                newPath.push_back(nextCom);
                pathQueue.push(newPath);
                visited[nextCom] = true;
            }
        }
    }
    return paths;
}

void init(int N, int mFileCnt[], int mFileID[][50], int mFileSize[][50]) {
    ::N = N; currentTime = 0;
    links.clear(); linkIdToIndex.clear(); fileLocations.clear();
    for (int i = 0; i < MAX_N; ++i) {
        computerFiles[i].clear(); linksByComputer[i].clear();
    }
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j < mFileCnt[i]; ++j) {
            File file = { mFileID[i][j], mFileSize[i][j], 0, 0 };
            computerFiles[i + 1][mFileID[i][j]] = file;
            fileLocations[mFileID[i][j]].push_back({ i + 1, mFileSize[i][j] });
        }
    }
}
void makeNet(int K, int mID[], int mComA[], int mComB[], int mDis[]) {
    for (int i = 0; i < K; ++i) {
        Link link = { mID[i], mComA[i], mComB[i], mDis[i], 0 };
        links.push_back(link);
        linkIdToIndex[mID[i]] = links.size() - 1;
        linksByComputer[mComA[i]].push_back(mID[i]);
        linksByComputer[mComB[i]].push_back(mID[i]);
    }
}

void removeLink(int mTime, int mID) {
    currentTime = mTime;
    if (linkIdToIndex.count(mID)) links[linkIdToIndex[mID]].removeTime = mTime;
}

int downloadFile(int mTime, int mComA, int mFileID) {
    currentTime = mTime;
    if (fileLocations.find(mFileID) == fileLocations.end()) return 0;
    vector<vector<int>> downloadPaths;
    for (const auto& location : fileLocations[mFileID]) {
        int fileCom = location.first;
        auto paths = findDownloadPaths(mComA, fileCom);
        downloadPaths.insert(downloadPaths.end(), paths.begin(), paths.end());
    }
    if (downloadPaths.empty()) return 0;
    int fileSize = computerFiles[fileLocations[mFileID][0].first][mFileID].size;
    int requiredTime = (fileSize + DOWNLOAD_RATE * MAX_DOWNLOAD_PATH - 1) / (DOWNLOAD_RATE * MAX_DOWNLOAD_PATH);
    computerFiles[mComA][mFileID] = { mFileID, fileSize, fileSize, mTime + requiredTime};
    return min((int)downloadPaths.size(), MAX_DOWNLOAD_PATH);
}

int getFileSize(int mTime, int mComA, int mFileID) {
    currentTime = mTime;
    if (computerFiles[mComA].count(mFileID)) return computerFiles[mComA][mFileID].size;
    return 0;
}

sample_input.txt
1 100
20
0 8 6
12345 7482
582509 1827
53729 9917
3284 3912
356712 4827
717077 2381
1 0
3 0 2 1
2 0 3
1 0
2 4 3
2 3 5
2 3 2
2 0 2
1 7
11 1 2 1
22 2 4 2
33 4 3 3
44 4 6 1
55 5 6 2
66 6 7 1
88 8 5 3
3 10 6 12345 5
4 13 6 212959 0
3 14 6 98765 0
3 15 6 53729 3
4 18 6 12345 360
4 25 6 12345 675
3 31 7 12345 4
4 35 7 12345 144
2 43 22
4 45 6 12345 1539
4 49 7 12345 540
3 50 4 3284 4
4 53 4 3284 108
3 61 4 53729 1
3 71 7 356712 1
4 73 4 3284 828
4 85 7 356712 126
3 520 5 12345 4
```
