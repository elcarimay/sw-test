```cpp
#if 1
#include <queue>
#include <vector>
using namespace std;

#define MAX_WORDS 50001
#define MAX_ROWS 200000
#define MAX_COLS 1000
#define MAX_BLOCKS 225
#define INF 0x7fffffff
#define ERASED 1

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a < b ? a : b; }
inline int ceil(int a, int b) { return (a + b - 1)/b; }

struct Word
{
    int row, col, len, state;
}words[MAX_WORDS];

struct Position
{
    int col, len;
    bool operator<(const Position& pos)const { return col > pos.col; }
};

struct WordPad
{
    priority_queue<Position> Q;
    int maxLen;
    void update() {
        maxLen = 0;
        vector<Position> popped;
        while (!Q.empty()) {
            auto cur = Q.top(); Q.pop();
            while (!Q.empty() && cur.col + cur.len == Q.top().col) {
                cur.len += Q.top().len; Q.pop();
            }
            maxLen = max(maxLen, cur.len);
            popped.push_back(cur);
        }
        for (auto& cur : popped) Q.push(cur);
    }
    void push(const Position& pos) {
        Q.push(pos);
        update();
    }
    Position query(int mLen) {
        Position res = { -1, 0 };
        vector<Position> popped;
        while (!Q.empty()) {
            auto cur = Q.top(); Q.pop();
            if (cur.len >= mLen) { res = cur; break; }
            else popped.push_back(cur);
        }
        for (auto& cur : popped) Q.push(cur);
        update();
        return res;
    }
}pad[MAX_ROWS];

struct Partition
{
    struct Block
    {
        int idx, maxlen;
    };
    int N, bSize, bCnt;
    Block blocks[MAX_BLOCKS];
    void init(int _N) {
        N = _N;
        bSize = sqrt(N);
        bCnt = ceil(N, bSize);
        for (int i = 0; i < bCnt; i++) blocks[i] = {};
    }
    void update(int row) {
        int bIdx = row / bSize;
        int left = bIdx * bSize;
        int right = min((bIdx + 1) * bSize - 1, N - 1);
        if (row == blocks[bIdx].idx) {
            blocks[bIdx].maxlen = -INF;
            for (int i = left; i <= right; i++)
                if (blocks[bIdx].maxlen < pad[i].maxLen)
                    blocks[bIdx] = { i, pad[i].maxLen };
        }
        else if (blocks[bIdx].maxlen < pad[row].maxLen)
            blocks[bIdx] = { row, pad[row].maxLen };
    }
    int query(int mLen) {
        int bIdx = -1;
        for(int i = 0;i < bCnt;i++)
            if (blocks[i].maxlen >= mLen) {
                bIdx = i; break;
            }
        if (bIdx == -1) return -1;
        int left = bIdx * bSize;
        int right = min((bIdx + 1) * bSize - 1, N - 1);
        for (int i = left; i <= right; i++)
            if (pad[i].maxLen >= mLen) return i;
    }
}P;

void init(int N, int M){
    for (int i = 0; i < MAX_WORDS; i++) words[i] = {};
    P.init(N);
    for (int i = 0; i < N; i++) {
        pad[i] = {};
        pad[i].push({ 0,M });
        P.update(i);
    }
}

int writeWord(int mId, int mLen){
    int row = P.query(mLen);
    if (row == -1) return -1;
    auto pos = pad[row].query(mLen);
    P.update(row);
    words[mId] = { row,pos.col,mLen };
    if (pos.len > mLen) {
        pad[row].push({ pos.col + mLen, pos.len - mLen });
        P.update(row);
    }
    return row;
}

int eraseWord(int mId){
    if ((words[mId].state == ERASED) || (words[mId].len == 0)) return -1;
    auto& word = words[mId];
    pad[word.row].push({ word.col, word.len });
    P.update(word.row);
    word.state = ERASED;
    word.len = 0;
    return word.row;
}
#endif
```
