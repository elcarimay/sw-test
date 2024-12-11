```cpp
#if 1
#define MAX_ROW 40
#define MAX_COL 30

#include <unordered_map>
#include <vector>
using namespace std;

int frame[5][3][3] = {
    {{1,1,0},{0,0,0},{0,0,0}},
    {{1,1,1},{0,0,0},{0,0,0}},
    {{1,0,0},{1,0,0},{1,0,0}},
    {{1,1,0},{0,1,1},{0,0,0}},
    {{1,0,0},{1,1,1},{0,0,1}}
};

int type(int key) {
    if (key == 110000000) return 0;
    if (key == 111000000) return 1;
    if (key == 100100100) return 2;
    if (key == 110011000) return 3;
    if (key == 100111001) return 4;
}

int R, C;
struct Result {
    int row, col;
};

unordered_map<int, vector<Result>> hmap; // hash, position(놓을수 있는 자리)

int getHash(int puzzle[3][3]) {
    int key = 0;
    for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++) {
        key += puzzle[i][j]; key *= 10;
    }
    return key / 10;
}

bool visit[MAX_ROW][MAX_COL];
bool isPossible(int r, int c, int(*puzzle)[3]) {
    for (int i = r; i < r + 3; i++) for (int j = c; j < c + 3; j++)
        if ((i >= R || j >= C) && (puzzle[i - r][j - c] == 1)) return false;
    return true;
}

int cells[MAX_ROW][MAX_COL];
void init(int mRows, int mCols, int mCells[MAX_ROW][MAX_COL]) {
    R = mRows, C = mCols; hmap.clear();
    for (int i = 0; i < 5; i++) {
        int key = getHash(frame[i]);
        for (int r = 0; r < mRows; r++) for (int c = 0; c < mCols; c++)
            if (isPossible(r, c, frame[i])) hmap[key].push_back({ r,c });
    }
    for (int r = 0; r < mRows; r++) for (int c = 0; c < mCols; c++) {
        cells[r][c] = mCells[r][c];
        visit[r][c] = false;
    }
}

int getpuzzleHash(int(*puzzle)[3]) {
    int key = 0;
    for (int r = 0; r < 3; r++) for (int c = 0; c < 3; c++) {
        if (puzzle[r][c]) key += 1;
        key *= 10;
    }
    return key / 10;
}

bool visited(int r, int c, int t) {
    for (int i = r; i < r + 3; i++) for (int j = c; j < c + 3; j++) {
        if (i >= R || j >= C) continue;
        if (frame[t][i - r][j - c] == 0) continue;
        if (visit[i][j]) return true;
    }
    return false;
}

Result putPuzzle(int mPuzzle[3][3]) {
    Result ret = { -1, -1 };
    int min_v1 = INT_MAX, min_v2;
    int key = getpuzzleHash(mPuzzle);
    int t = type(key);
    for (int r = 0; r < 3; r++) for (int c = 0; c < 3; c++) {
        if (frame[t][r][c] == 0) continue;
        min_v1 = min(min_v1, mPuzzle[r][c]);
    }
    min_v1--;
        
    for (auto nx : hmap[key]) {
        min_v2 = INT_MAX;
        if (visited(nx.row, nx.col, t)) continue;
        for (int r = nx.row; r < nx.row + 3; r++) for (int c = nx.col; c < nx.col + 3; c++) {
            if (frame[t][r - nx.row][c - nx.col] == 0) continue;
            min_v2 = min(min_v2, cells[r][c]);
        }
        min_v2--;
        bool flag = true;
        for (int r = nx.row; r < nx.row + 3; r++) for (int c = nx.col; c < nx.col + 3; c++) {
            if (frame[t][r - nx.row][c - nx.col] == 0) continue;
            if ((mPuzzle[r - nx.row][c - nx.col] - min_v1) == (cells[r][c] - min_v2)) continue;
            else flag = false;
        }
        if (flag) {
            for (int r = nx.row; r < nx.row + 3; r++) for (int c = nx.col; c < nx.col + 3; c++) {
                if (frame[t][r - nx.row][c - nx.col] == 0) continue;
                else visit[r][c] = true;
            }
            ret = { nx.row, nx.col }; return ret;
        }
    }
    return ret;
}

void clearPuzzles() {
    for (int r = 0; r < R; r++) for (int c = 0; c < C; c++) visit[r][c] = false;
}
#endif // 1


```
