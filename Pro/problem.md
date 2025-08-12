[문제 설명]
N x N 의 격자판 위에서 지렁이 게임이 진행된다.
[Fig. 1] 에서 한 변의 길이 N의 값이 10인 격자판을 확인할 수 있다.
게임에 참여하는 유저들은 고유한 ID를 가지며 특정 시간(mTime, 단위: 초)에 특정 위치에서(mX, mY) 게임을 시작한다.
초기 지렁이 길이(mLength)가 주어지며, 게임에 참가하게 되면 지렁이가 바라보는 방향은 항상 격자판의 상단을 바라보며 추가된다.
[Fig. 2] 에서 시점이 1초일 때 (2, 3) 위치에 ID가 1이며, 길이가 4인 지렁이가 추가된 것을 확인할 수 있으며, ID가 표시된 위치가 지렁이의 머리이다.
게임은 다음과 같은 규칙으로 진행된다.
모든 지렁이는 매초마다 머리가 바라보는 방향으로 1칸씩 전진하며, 만약 몸이 일직선으로 펴져 있는 경우, 바라보는 방향을 시계 방향으로 90도 회전 후에 1칸 전진한다.
[Fig. 3] 에서 시간의 흐름에 따른 지렁이의 위치를 확인할 수 있다.
모든 지렁이는 동시에 이동하며, 이동을 마친 후 다음 규칙에 따라 소멸하거나 성장 잠재력을 얻는다.
- 격자판을 벗어난 지렁이는 소멸한다.
- A 지렁이가 B 지렁이에 충돌한 경우, A 지렁이는 소멸하며 B 지렁이는 A 지렁이의 길이만큼 성장 잠재력이 증가한다.
- A 지렁이와 B 지렁이가 서로 머리가 충돌한 경우 두 지렁이는 모두 소멸한다.
- A 지렁이가 B 지렁이에 충돌하고, B 지렁이가 C 지렁이에 충돌한 경우, A 지렁이와 B 지렁이는 모두 소멸하며, C 지렁이는 B 지렁이의 길이만큼만 성장 잠재력이 증가한다.
- 이동 후 위 조건을 만족하는 모든 지렁이들은 동시에 소멸한다.
[Fig. 4] 에서 2번 지렁이가 이동 후 격자판 밖으로 벗어나 소멸하는 것을 확인할 수 있다.
[Fig. 5] 에서 지렁이들이 이동 후 동시에 소멸하는 것을 확인할 수 있다.
3번 지렁이는 4번 지렁이에 충돌하고 4번 지렁이는 5번 지렁이에 충돌하여 소멸한다.
5번 지렁이는 4번 지렁이의 길이인 4 만큼 성장 잠재력을 획득한다. 성장 잠재력은 지렁이 꼬리 부분의 대괄호 표시를 통해 확인할 수 있다.
6번 지렁이와 7번 지렁이는 서로 충돌하여 두 지렁이 모두 소멸한다.
8번 지렁이와 9번 지렁이는 서로의 머리가 충돌하여 두 지렁이 모두 소멸한다.
모든 지렁이는 성장 잠재력이 1 이상인 경우, 매초 이동 시 길이가 1씩 성장하고, 성장 잠재력은 1씩 감소한다.
[Fig. 6] 에서 5번 지렁이가 [Fig. 5] 에서의 충돌 후 시간의 흐름에 따라 성장하는 것을 확인할 수 있다.
[Fig. 7] 에서 지렁이들이 이동 후 동시에 소멸하는 것을 확인할 수 있다.
10번 지렁이는 이동 후 격자판을 벗어났고, 11번 지렁이는 이동 후 10번 지렁이와 충돌하여 두 지렁이 모두 소멸한다.
12번 지렁이는 이동 후 충돌이 발생하지 않아 정상적으로 전진한다.

게임에 유저들이 계속해서 참여할 때, 특정 시점에 랭킹을 확인할 수 있는 프로그램을 작성해야 한다.
아래 API 설명을 참조하여 각 함수를 구현하라.

void init(int N)
각 테스트 케이스의 처음에 호출된다.
격자판 한 변의 길이 N이 주어진다.
초기 시간은 0초부터 시작하며, 이때 격자판 공간 위에는 지렁이가 존재하지 않는다.
Parameters
N : 격자판의 한 변의 길이 (10 ≤ N ≤ 2,000)

void join(int mTime, int mID, int mX, int mY, int mLength)
mTime 시간에 ID가 mID이고 길이가 mLength인 지렁이가 (mX, mY) 위치부터 추가된다.
mTime 시간에 모든 지렁이의 이동 및 충돌이 이루어진 후에 추가가 이루어 진다.
지렁이는 항상 격자판의 상단을 바라보는 상태로, 격자판을 벗어나지 않는 위치에 추가된다.
지렁이가 추가되는 위치에는 다른 지렁이가 존재하지 않음을 보장한다.
함수 호출 시 전달되는 mID 값은 이전 호출에서 이미 전달된 값이 아님을 보장한다.
Parameters
mTime : 현재 시간 (0 ≤ mTime ≤ 10,000)
mID : 지렁이의 ID (1 ≤ mID ≤ 1,000,000,000)
mX : 지렁이가 추가 된 X 좌표 (0 ≤ mX ≤ N - 1)
mY : 지렁이가 추가 된 Y 좌표 (0 ≤ mY ≤ N - 1)
mLength : 초기 지렁이의 길이 (3 ≤ mLength ≤ 1,000)

RESULT top5(int mTime)
mTime 시점에 랭킹이 높은 순서로 최대 5마리의 지렁이 ID를 RESULT 구조체에 저장하고 반환한다.
mTime 시간에 모든 지렁이의 이동 및 충돌이 이루어진 후에 지렁이의 랭킹을 반환한다.
랭킹의 기준은 다음과 같다.
지렁이의 길이가 긴 지렁이의 랭킹이 더 높다.
지렁이의 길이가 같을 경우, ID가 큰 지렁이의 랭킹이 더 높다.
반환할 때 저장된 지렁이의 수를 RESULT.cnt에 저장하고 i번쨰 지렁이의 ID를 RESULT.IDs[I - 1]에 저장한다. (1 ≤ i ≤ RESULT.cnt)
격자판 위에 지렁이가 없는 경우 RESULT.cnt에 0을 저장한다.
Parameters
mTime : 현재 시간 (0 ≤ mTime ≤ 10,000)
Returns
RESULT.cnt : 랭킹이 5위 안에 속하는 지렁이의 수
RESUTL.IDs : 랭킹이 5위 안에 속하는 지렁이들의 ID

// gcc -O2 -std=c++17
#include <bits/stdc++.h>
using namespace std;

struct RESULT { int cnt; long long IDs[5]; };

struct Run {
    int dir; // 0:up,1:right,2:down,3:left (head 방향 기준으로 앞쪽 run이 front)
    int len;
};

struct Worm {
    long long id;
    bool alive = true;
    int N;
    int hx, hy;      // head
    int tx, ty;      // tail
    int dir = 0;     // facing
    int length = 0;
    int grow = 0;    // growth potential
    deque<Run> runs; // head-side at front

    void placeStraight(int x, int y, int L, int face, int N_) {
        N = N_; id = -1; alive = true;
        hx = x; hy = y; dir = face; length = L; grow = 0;
        // 몸은 머리에서 '뒤로' 일직선으로 L-1칸
        int bdir = (face + 2) % 4; // tail쪽 방향
        runs.clear();
        runs.push_front({face, 1}); // 머리 칸 1
        if (L - 1 > 0) runs.push_back({bdir, L - 1});
        // 꼬리 좌표 계산
        static int dx[4] = {0, 1, 0, -1};
        static int dy[4] = {-1, 0, 1, 0};
        tx = x + dx[bdir] * (L - 1);
        ty = y + dy[bdir] * (L - 1);
    }

    // 미리보기: 몸이 일직선인지?
    bool isStraight() const { return runs.size() == 1; }

    // 한 칸 전진(+성장 여부 결정)은 외부에서 grid반영 전에 '새 머리/빠질 꼬리'만 예측.
    // 실제 이동/런 갱신은 충돌 생존 여부 확정 후 applyMove()에서 수행한다.
    pair<int,int> previewNextHead() const {
        int ndir = isStraight() ? (dir + 1) % 4 : dir;
        static int dx[4] = {0, 1, 0, -1};
        static int dy[4] = {-1, 0, 1, 0};
        return {hx + dx[ndir], hy + dy[ndir]};
    }
    int previewNextDir() const { return isStraight() ? (dir + 1) % 4 : dir; }

    // 이번 초에 꼬리 빠지는 칸 (성장X일 때만 의미)
    pair<int,int> previewTailPopCell() const {
        if (runs.empty()) return {tx, ty};
        // tail run의 진행 방향으로 한 칸 앞으로
        int tdir = runs.back().dir;
        static int dx[4] = {0, 1, 0, -1};
        static int dy[4] = {-1, 0, 1, 0};
        return {tx + dx[tdir], ty + dy[tdir]};
    }

    // 실제 이동 적용(충돌에서 살아남은 경우에만 호출)
    // gridMark(head/tail)은 바깥에서 처리
    void applyMove(bool willGrow) {
        int ndir = previewNextDir();
        // head 쪽 run 갱신
        if (!runs.empty() && runs.front().dir == ndir) runs.front().len++;
        else runs.push_front({ndir, 1});
        // 새 head 좌표
        static int dx[4] = {0, 1, 0, -1};
        static int dy[4] = {-1, 0, 1, 0};
        hx += dx[ndir]; hy += dy[ndir];
        dir = ndir;
        // tail 처리
        if (willGrow) {
            length++; // 성장 반영
            grow--;
        } else {
            // 꼬리 한 칸 줄이기
            if (!runs.empty()) {
                runs.back().len--;
                int tdir = runs.back().dir;
                if (runs.back().len == 0) runs.pop_back();
                // tx,ty를 tdir로 한 칸 전진
                tx += dx[tdir]; ty += dy[tdir];
            }
        }
    }
};

static const int MAXN = 2000; // N<=2000
static int N_global, curTime;

// 격자: -1이면 비어있음, 아니면 내부 wormIdx
static int owner[2001][2001];

static vector<Worm> worms;            // 내부 인덱스: 0..W-1
static unordered_map<long long,int> id2idx; // 외부 ID → 내부 인덱스

// 유틸
inline bool inBounds(int x,int y){ return (0<=x && x<N_global && 0<=y && y<N_global); }

void clearWormOnGrid(int widx){
    Worm &w = worms[widx];
    if(!w.alive) return;
    // 몸통 모든 칸을 지우기: runs를 따라가며 칸을 순회
    int x = w.hx, y = w.hy;
    // head에서 runs를 따라 tail로 내려가면 좌표 계산이 번거롭다.
    // tail에서 역으로 올라가도 동일. 여기선 tail→head로 간다.
    int cx = w.tx, cy = w.ty;
    owner[cx][cy] = -1;
    static int dx[4] = {0, 1, 0, -1};
    static int dy[4] = {-1, 0, 1, 0};
    // tail부터 runs를 복사해 따라감
    vector<Run> seq(w.runs.begin(), w.runs.end());
    // tail 방향은 seq.back().dir의 반대이므로, tail→head로 이동하려면
    // tail run부터 거꾸로 진행하되 각 run의 '반대' 방향으로 len칸 이동
    // 구현을 단순화하려고, w의 모든 칸을 BFS처럼 다시 찍는 것보다,
    // 시뮬레이션 중 항상 head/tail 증감으로 owner를 동기화하므로
    // 소멸 시에는 ‘현재 칸들’을 한 번에 전부 지우는 대신
    // 이 함수는 **안전 상** 전체 클리어가 필요할 때만 쓰고,
    // 보통은 매 초 이동에서 증분 업데이트(새 머리 + 빠진 꼬리)만 합니다.
    // 여기서는 정확성을 위해 전체 지우기를 수행하되, 좌표를 재구축:
    // head에서 역추적: runs(front→back)대로 head→tail.
    int hx = w.hx, hy = w.hy;
    owner[hx][hy] = -1;
    int px = hx, py = hy;
    for(size_t i=0;i<seq.size();++i){
        int d = seq[i].dir;
        for(int k=1;k<=seq[i].len- (i==0 ? 1:0); ++k){ // head run은 head 칸 이미 처리했으니 -1
            px -= dx[d]; py -= dy[d];
            if (inBounds(px,py)) owner[px][py] = -1;
        }
    }
    w.alive = false;
}

void init(int N){
    N_global = N; curTime = 0;
    for(int x=0;x<N;x++) for(int y=0;y<N;y++) owner[x][y] = -1;
    worms.clear(); id2idx.clear();
}

void advanceTo(int t){
    static int dx[4] = {0, 1, 0, -1};
    static int dy[4] = {-1, 0, 1, 0};
    for (; curTime < t; ++curTime){
        // 수집 단계
        int W = (int)worms.size();
        vector<int> willGrow(W, 0), ndir(W, 0);
        vector<pair<int,int>> newHead(W, {-1,-1});
        vector<pair<int,int>> tailPop(W, {-1,-1});
        vector<char> considered(W, 0);

        for(int i=0;i<W;i++){
            if(!worms[i].alive) continue;
            considered[i]=1;
            willGrow[i] = (worms[i].grow>0);
            ndir[i] = worms[i].previewNextDir();
            newHead[i] = worms[i].previewNextHead();
            if(!willGrow[i]) tailPop[i] = worms[i].previewTailPopCell();
        }

        // 1) 머리-머리 head-on
        unordered_map<long long, vector<int>> headCell;
        headCell.reserve(considered.size()*2);
        auto key = [&](int x,int y)->long long { return ( (long long)x<<21 ) ^ (long long)y; };
        vector<char> dead(W, 0);

        for(int i=0;i<W;i++) if(considered[i]){
            auto [x,y] = newHead[i];
            headCell[key(x,y)].push_back(i);
        }
        for (auto &kv : headCell){
            auto &vec = kv.second;
            if(vec.size()>=2){
                for(int i: vec) dead[i]=1; // head-on → 모두 소멸
            }
        }
        // 2) 경계 밖
        for(int i=0;i<W;i++) if(considered[i] && !dead[i]){
            auto [x,y] = newHead[i];
            if(!inBounds(x,y)) dead[i]=1;
        }

        // 3) 머리-몸체 충돌(“동시 이동 후” 관점으로 tailPop 칸은 비어있는 것으로 간주)
        // 충돌 그래프 A->B (A가 B의 몸에 부딪힘). 여기서 B가 이번 초에 생존할 때만 B에게 성장잠재력 부여.
        vector<int> hitTo(W, -1); // -1: no hit, else owner index
        for(int i=0;i<W;i++) if(considered[i] && !dead[i]){
            auto [x,y] = newHead[i];
            if(!inBounds(x,y)) continue; // 이미 oob는 dead 처리됨
            int own = (x>=0 && x<N_global && y>=0 && y<N_global) ? owner[x][y] : -1;
            if(own==-1) continue;
            // tail-pop 면 충돌 아님
            if(!willGrow[own] && tailPop[own].first==x && tailPop[own].second==y) continue;
            // 자기 몸에 부딪혀도 소멸(단, 자기 꼬리 pop 칸이면 위에서 걸러짐)
            hitTo[i] = own;
            dead[i] = 1;
        }

        // 생존 여부 확정 후 성장잠재력 부여
        vector<int> addGrow(W, 0);
        for(int i=0;i<W;i++){
            if(hitTo[i]!=-1){
                int tgt = hitTo[i];
                if(considered[tgt] && !dead[tgt]){
                    addGrow[tgt] += worms[i].length;
                }
            }
        }

        // 4) 실제 이동/격자 반영
        // 4-1) 살아있는 것들: owner 새 머리 세팅 & 꼬리 pop 반영
        for(int i=0;i<W;i++) if(considered[i] && !dead[i]){
            // 새 머리 칸은 비어있거나(원래 비어있음/상대 꼬리 pop)여야 한다.
            // 먼저 꼬리 비우기
            if(!willGrow[i]){
                auto [tx,ty] = worms[i].previewTailPopCell();
                if(inBounds(tx,ty)) owner[tx][ty] = -1;
            }
        }
        for(int i=0;i<W;i++) if(considered[i] && !dead[i]){
            auto [hx,hy] = newHead[i];
            if(inBounds(hx,hy)) owner[hx][hy] = i;
        }
        // 4-2) 워ーム 내부 상태 갱신
        for(int i=0;i<W;i++) if(considered[i] && !dead[i]){
            worms[i].applyMove(willGrow[i]);
        }

        // 4-3) 소멸자들: 격자에서 완전 제거
        for(int i=0;i<W;i++) if(considered[i] && dead[i]){
            clearWormOnGrid(i);
        }

        // 5) 성장잠재력 획득 반영(다음 초부터 성장)
        for(int i=0;i<W;i++) if(considered[i] && worms[i].alive){
            worms[i].grow += addGrow[i];
        }
    }
}

void join(int mTime, int mID, int mX, int mY, int mLength){
    advanceTo(mTime);
    // 새 지렁이 배치: 머리 (mX,mY), 위를 봄(dir=0), 몸은 아래쪽으로 mLength-1칸
    int idx = (int)worms.size();
    worms.push_back(Worm());
    Worm &w = worms.back();
    w.placeStraight(mX, mY, mLength, 0, N_global);
    w.id = mID;

    // 격자 점유 확인(문제에서 '겹치지 않음' 보장)
    // owner 찍기
    // head부터 tail까지 run을 따라 칸 채우기
    // head
    owner[mX][mY] = idx;
    // head run 나머지
    static int dx[4] = {0, 1, 0, -1};
    static int dy[4] = {-1, 0, 1, 0};
    int x=mX, y=mY;
    if (w.runs.size()>=1){
        int d = w.runs[0].dir;
        for(int k=1;k<w.runs[0].len;k++){
            x -= dx[d]; y -= dy[d];
            owner[x][y] = idx;
        }
    }
    // 그 다음 run들
    for(size_t r=1;r<w.runs.size();++r){
        int d = w.runs[r].dir;
        for(int k=0;k<w.runs[r].len;k++){
            x -= dx[d]; y -= dy[d];
            owner[x][y] = idx;
        }
    }
    id2idx[mID] = idx;
}

RESULT top5(int mTime){
    advanceTo(mTime);
    // 살아있는 지렁이 top5: 길이 내림차순, 길이 같으면 ID 내림차순
    struct Node{ int len; long long id; };
    auto cmp = [](const Node& a, const Node& b){
        if(a.len!=b.len) return a.len > b.len;
        return a.id > b.id;
    };
    // size≤5 최소 힙을 쓰려면 반대 비교
    auto cmpMin = [](const Node& a, const Node& b){
        if(a.len!=b.len) return a.len > b.len; // len 큰 게 '더 앞'이므로, 최소힙 기준으로는 len 작은 게 위로 가야 하니 > 사용
        return a.id > b.id;
    };
    priority_queue<Node, vector<Node>, decltype(cmpMin)> pq(cmpMin);

    for (auto &w : worms){
        if(!w.alive) continue;
        Node node{w.length, w.id};
        if ((int)pq.size() < 5) pq.push(node);
        else{
            // 현재 top5의 최솟값과 비교
            Node curMin = pq.top();
            // 순위 규칙: 길이 큰/같으면 ID 큰 것이 우선
            bool better = (node.len > curMin.len) || (node.len==curMin.len && node.id > curMin.id);
            if (better){ pq.pop(); pq.push(node); }
        }
    }
    // pq에는 5개 이하가 '오름차순'으로 들어가 있으므로, 꺼내서 역순 정렬
    vector<Node> v;
    while(!pq.empty()){ v.push_back(pq.top()); pq.pop(); }
    sort(v.begin(), v.end(), cmp);

    RESULT R; R.cnt = (int)v.size();
    for(int i=0;i<R.cnt;i++) R.IDs[i] = v[i].id;
    return R;
}

