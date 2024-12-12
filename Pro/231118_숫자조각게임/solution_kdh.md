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

int R, C;
struct Result {
    int row, col;
};

unordered_map<int, vector<Result>> hmap; // hash, position(놓을수 있는 자리)

int getHash(int p[3][3], int t) {
    int min_v = 5;
    for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++)
        if (frame[t][i][j]) min_v = min(min_v, p[i][j]);
    min_v--; int key = 0;
    for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++) {
        if (frame[t][i][j]) key += (p[i][j] - min_v);
        if (i == 2 && j == 2) continue;
        key *= 10;
    }
    return key;
}

bool isPossible(int r, int c, int(*p)[3]) {
    for (int i = r; i < r + 3; i++) for (int j = c; j < c + 3; j++)
        if ((i >= R || j >= C) && (p[i - r][j - c] == 1)) return false;
    return true;
}

bool visit[MAX_ROW][MAX_COL];
void init(int mRows, int mCols, int mCells[MAX_ROW][MAX_COL]) {
    R = mRows, C = mCols; hmap.clear();
    int puzzle[3][3];
    for (int t = 0; t < 5; t++)
        for (int r = 0; r < mRows; r++) for (int c = 0; c < mCols; c++) {
            if (!isPossible(r, c, frame[t])) continue;
            for (int i = r; i < r + 3; i++) for (int j = c; j < c + 3; j++)
                if (frame[t][i - r][j - c]) puzzle[i - r][j - c] = mCells[i][j];
            int key = getHash(puzzle, t);
            hmap[key].push_back({ r,c });
        }
    for (int r = 0; r < mRows; r++) for (int c = 0; c < mCols; c++)
        visit[r][c] = false;
}

int getpuzzleHash(int p[3][3]) {
    int min_v = 5;
    for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++)
        if (p[i][j] != 0) min_v = min(min_v, p[i][j]);
    min_v--; int key = 0;
    for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++) {
        if (p[i][j] != 0) key += (p[i][j] - min_v);
        if (i == 2 && j == 2) continue;
        key *= 10;
    }
    return key;
}

bool visited(int r, int c, int p[3][3]) {
    for (int i = r; i < r + 3; i++) for (int j = c; j < c + 3; j++)
        if (p[i - r][j - c] != 0 && visit[i][j] == 1) return true;
    return false;
}

Result putPuzzle(int mPuzzle[3][3]) {
    int key = getpuzzleHash(mPuzzle);
       
    for (auto nx : hmap[key])
        if (!visited(nx.row, nx.col, mPuzzle)){
            for (int r = nx.row; r < nx.row + 3; r++) for (int c = nx.col; c < nx.col + 3; c++)
                if (mPuzzle[r - nx.row][c - nx.col]) visit[r][c] = true;
            return { nx.row, nx.col };
        }
    return { -1, -1 };
}

void clearPuzzles() {
    for (int r = 0; r < R; r++) for (int c = 0; c < C; c++) visit[r][c] = false;
}
#endif // 1
```
