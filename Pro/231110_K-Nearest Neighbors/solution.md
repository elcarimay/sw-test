```cpp
/*
TS KNN_ver1
- 0부터 id 부여
- 그룹별로 부여한 id 기록
- 부여한 id별로 (x,y,c) 기록
- 삭제시 해당되는 그룹만 탐색
*/
#include<unordered_map>
#include<vector>
#include<algorithm>
using namespace std;
 
int K, L, G;    // G = 그룹 최대 번호(4000/L)
 
int idcnt;
unordered_map<int, int> idmap;
 
struct Dot {
    int x, y, c;
}dot[20005];
 
vector<int> V[1005][1005];
 
void init(int k, int l)
{
    idcnt = 0;
    idmap.clear();
 
    K = k, L = l, G = 4000 / l;
    for (int i = 0; i <= G; i++) for (int j = 0; j <= G; j++) V[i][j].clear();
}
 
void addSample(int mID, int mx, int my, int mc)
{
    int mid = idmap[mID] = idcnt++;
    dot[mid] = { mx,my,mc };
    V[mx / L][my / L].push_back(mid);
}
 
void deleteSample(int mID)
{
    int mid = idmap[mID];
    auto& v = V[dot[mid].x / L][dot[mid].y / L];
    v.erase(find(v.begin(), v.end(), mid));
}
 
struct Candi {
    int dist, x, y, c;
    bool operator<(const Candi& r) const {
        if (dist != r.dist) return dist < r.dist;
        if (x != r.x) return x < r.x;
        return y < r.y;
    }
}candi[20005];
 
int predict(int mx, int my)
{
    // 탐색 그룹 범위 설정
    int sX = max(0, mx / L - 1);
    int sY = max(0, my / L - 1);
    int eX = min(mx / L + 1, G);
    int eY = min(my / L + 1, G);
 
    // 거리 L 이하인 자료 후보 선정
    int candi_cnt = 0;
    for (int i = sX; i <= eX; i++)
        for (int j = sY; j <= eY; j++)
            for (int mid : V[i][j]) {
                int dist = abs(dot[mid].x - mx) + abs(dot[mid].y - my);
                if (dist <= L) candi[candi_cnt++] = { dist,dot[mid].x,dot[mid].y,dot[mid].c };
            }
 
    // 후보가 K개가 안되면 이상치
    if (candi_cnt < K) return -1;
 
    // K-최근접 이웃 선정
    partial_sort(candi, candi + K, candi + candi_cnt);
 
    // 범주 추정
    int cnt[15] = {};
    for (int i = 0; i < K; i++) cnt[candi[i].c]++;
    return max_element(cnt + 1, cnt + 11) - cnt;
}
```
