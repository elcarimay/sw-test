#include <vector>
#include <unordered_map>
#include <cstring>
#include <algorithm>

using namespace std;

static int G_N, G_K;

// 현재 샘플 보관: id -> (x,y,c)
struct Sample { int x, y, c; };
static unordered_map<long long, Sample> idmap;

// 격자상의 카테고리별 개수 (fit 전까지 누적)
static int gridCnt[6][128][128]; // [1..K][0..N-1][0..N-1]

// fit 시점의 카테고리별 2D prefix sums
static int ps[6][129][129]; // [1..K][0..N]×[0..N]

// 트리 노드
struct Node {
    int x1, y1, x2, y2; // 영역 (포함 범위)
    int rep;            // 대표 범주 (leaf에서 사용)
    int left, right;    // 자식 인덱스 (없으면 -1)
    bool leaf;
    // split 정보 (예측용)
    // axis=0: vertical split at x<=split on left  / x>=split+1 on right
    // axis=1: horizontal split at y<=split on top / y>=split+1 on bottom
    int axis;           // 0 or 1, leaf면 의미없음
    int split;          // 경계 좌표 (x3 또는 y3)
};
static vector<Node> tree;
static int rootIdx = -1;

// ---------- 유틸: prefix sum ----------
static inline int rectCount(int cat, int x1, int y1, int x2, int y2){
    // 빈 영역 방어
    if (x1 > x2 || y1 > y2) return 0;
    int (*A)[129] = ps[cat];
    // ps는 [1..N] 인덱싱, 좌표는 [0..N-1]
    int X1 = x1, Y1 = y1, X2 = x2+1, Y2 = y2+1;
    return A[X2][Y2] - A[X1][Y2] - A[X2][Y1] + A[X1][Y1];
}

struct LossInfo {
    int loss;
    int repCat;
    int total;
};

// 사각형 영역의 손실/대표범주 계산
static inline LossInfo computeLoss(int x1, int y1, int x2, int y2){
    int total = 0;
    int bestCat = 1;
    int bestCnt = 0;
    for(int c=1;c<=G_K;++c){
        int cnt = rectCount(c, x1, y1, x2, y2);
        total += cnt;
        if (cnt > bestCnt || (cnt==bestCnt && c < bestCat)){
            bestCnt = cnt; bestCat = c;
        }
    }
    LossInfo li;
    li.loss = total - bestCnt;
    li.repCat = bestCat;
    li.total = total;
    return li;
}

// 분할 후보 평가 결과
struct SplitBest {
    bool ok;
    int axis;   // 0: vertical(x), 1: horizontal(y)
    int pos;    // split coord (x3 or y3)
    int childLossSum;
};

// 노드에서 최적 분할 찾기 (규칙 a,b,c 반영)
static SplitBest findBestSplit(int x1,int y1,int x2,int y2, int selfLoss){
    SplitBest ans; ans.ok=false; ans.childLossSum = selfLoss;

    int bestLoss = selfLoss; // 자식 합이 selfLoss보다 작아야 의미 있음
    int bestAxis = -1;
    int bestPos = -1;

    // 1) vertical splits: x in [x1, x2-1]
    int bestLossV = 1e9, bestXV = -1;
    if (x1 < x2){
        for(int x=x1; x<=x2-1; ++x){
            LossInfo L = computeLoss(x1,y1,x,y2);
            LossInfo R = computeLoss(x+1,y1,x2,y2);
            int sum = L.loss + R.loss;
            if (sum < bestLossV){ bestLossV = sum; bestXV = x; }
            else if (sum == bestLossV && x < bestXV){ bestXV = x; }
        }
    }

    // 2) horizontal splits: y in [y1, y2-1]
    int bestLossH = 1e9, bestYH = -1;
    if (y1 < y2){
        for(int y=y1; y<=y2-1; ++y){
            LossInfo T = computeLoss(x1,y1,x2,y);
            LossInfo B = computeLoss(x1,y+1,x2,y2);
            int sum = T.loss + B.loss;
            if (sum < bestLossH){ bestLossH = sum; bestYH = y; }
            else if (sum == bestLossH && y < bestYH){ bestYH = y; }
        }
    }

    // 전체 최솟값
    int candLoss = min(bestLossV, bestLossH);
    if (candLoss >= selfLoss) {
        ans.ok=false;
        return ans;
    }
    // 규칙 c: 동률이면 "가장 작은 x에서 vertical"을 우선, 그렇지 않으면 "가장 작은 y에서 horizontal"
    if (bestLossV == candLoss){
        ans.ok=true; ans.axis=0; ans.pos=bestXV; ans.childLossSum = bestLossV;
    } else {
        ans.ok=true; ans.axis=1; ans.pos=bestYH; ans.childLossSum = bestLossH;
    }
    return ans;
}

// 재귀 빌드: 노드 생성 후 리턴 인덱스와 리프 손실 합계 반환
static int buildNode(int x1,int y1,int x2,int y2, int &leafLossSum){
    Node nd; nd.x1=x1; nd.y1=y1; nd.x2=x2; nd.y2=y2;
    nd.left=-1; nd.right=-1; nd.axis=0; nd.split=0;

    LossInfo self = computeLoss(x1,y1,x2,y2);
    SplitBest sb = findBestSplit(x1,y1,x2,y2, self.loss);

    if (!sb.ok){
        nd.leaf = true;
        nd.rep = self.repCat;
        int idx = (int)tree.size(); tree.push_back(nd);
        leafLossSum += self.loss;
        return idx;
    }

    nd.leaf = false;
    nd.rep = self.repCat; // leaf 아니어도 예측 실패 시 참고 값은 아니지만 저장해둠
    nd.axis = sb.axis;
    nd.split = sb.pos;

    int idx = (int)tree.size();
    tree.push_back(nd);

    if (sb.axis == 0){
        // vertical: [x1..split] | [split+1..x2]
        int lidx = buildNode(x1,y1,sb.pos,y2, leafLossSum);
        int ridx = buildNode(sb.pos+1,y1,x2,y2, leafLossSum);
        tree[idx].left = lidx; tree[idx].right = ridx;
    } else {
        // horizontal: [y1..split] | [split+1..y2]
        int lidx = buildNode(x1,y1,x2,sb.pos, leafLossSum);
        int ridx = buildNode(x1,sb.pos+1,x2,y2, leafLossSum);
        tree[idx].left = lidx; tree[idx].right = ridx;
    }
    return idx;
}

// ---------- 공개 API ----------

void init(int N, int K){
    G_N = N; G_K = K;
    idmap.clear();
    memset(gridCnt, 0, sizeof(gridCnt));
    tree.clear(); rootIdx = -1;
}

void addSample(int mID, int mX, int mY, int mC){
    // 보장: 같은 ID는 처음, 해당 점에는 자료 없음
    idmap[(long long)mID] = {mX, mY, mC};
    gridCnt[mC][mX][mY] += 1;
}

void deleteSample(int mID){
    auto it = idmap.find((long long)mID);
    if (it == idmap.end()) return; // 안전장치
    Sample s = it->second;
    gridCnt[s.c][s.x][s.y] -= 1;
    idmap.erase(it);
}

int fit(){
    // 2D prefix sums 구축
    for(int c=1;c<=G_K;++c){
        // ps[c][x][y] for x∈[0..N], y∈[0..N]
        for(int x=0;x<=G_N;++x) for(int y=0;y<=G_N;++y) ps[c][x][y]=0;
        for(int x=0;x<G_N;++x){
            int rowSum = 0;
            for(int y=0;y<G_N;++y){
                rowSum += gridCnt[c][x][y];
                ps[c][x+1][y+1] = ps[c][x][y+1] + rowSum;
            }
        }
    }

    tree.clear();
    int leafLossSum = 0;
    rootIdx = buildNode(0,0,G_N-1,G_N-1, leafLossSum);
    return leafLossSum;
}

int predict(int mX, int mY){
    // fit 이후 호출됨 (보장)
    int cur = rootIdx;
    while (true){
        const Node &nd = tree[cur];
        if (nd.leaf) return nd.rep;
        if (nd.axis == 0){
            // vertical split by x
            if (mX <= nd.split) cur = nd.left;
            else                cur = nd.right;
        } else {
            // horizontal split by y
            if (mY <= nd.split) cur = nd.left;
            else                cur = nd.right;
        }
    }
}