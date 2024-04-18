```cpp
#include <vector>
#include <unordered_map>

#define MAX_ROW 40
#define MAX_COL 30

using namespace std;

int initmap[MAX_ROW][MAX_COL];
int map[MAX_ROW][MAX_COL];
int R, C, idx;

pair<int, int> answer;

struct Result {
    int row;
    int col;
};

unordered_map<int, int> stidx;

int min(int a, int b) {
    return a < b ? a : b;
}

struct searchtype {
    vector < pair<int, int>> rc;
};

searchtype stlist[4030];


void init(int mRows, int mCols, int mCells[MAX_ROW][MAX_COL])
{
    R = mRows;
    C = mCols;
    idx = 0;
    stidx.clear(); //idx 생성 및 초기화

    for (int i = 0; i < 4030; i++) {//구조체 초기화
        stlist[i].rc.clear();
    }

    int mindist;
    for (int i = 0; i < R; i++) {//맵 생성
        for (int j = 0; j < C; j++) {
            initmap[i][j] = mCells[i][j];
            map[i][j] = mCells[i][j];
        }
    }

    for (int i = 0; i < R; i++) {//index값 구조체 추가//type1 100000, type2 200000, type3 300000, type4 400000, type5 500000, 
        for (int j = 0; j < C; j++) {
            if (C - j > 1) { //type1
                mindist = map[i][j];
                mindist = min(mindist, map[i][j + 1]);
                idx = 100000 + ((map[i][j] - mindist) * 10) + map[i][j + 1] - mindist;

                if (stidx[idx] == 0) {
                    stidx[idx] = stidx.size();
                }
                stlist[stidx[idx]].rc.emplace_back(i, j);
            }

            if (C - j > 2) { //type2
                mindist = map[i][j];
                mindist = min(mindist, map[i][j + 1]);
                mindist = min(mindist, map[i][j + 2]);
                idx = 200000 + ((map[i][j] - mindist) * 100) + ((map[i][j + 1] - mindist) * 10) + map[i][j + 2] - mindist;

                if (stidx[idx] == 0) {
                    stidx[idx] = stidx.size();
                }
                stlist[stidx[idx]].rc.emplace_back(i, j);
            }

            if (R - i > 2) { //type3
                mindist = map[i][j];
                mindist = min(mindist, map[i + 1][j]);
                mindist = min(mindist, map[i + 2][j]);

                idx = 300000 + ((map[i][j] - mindist) * 100) + ((map[i + 1][j] - mindist) * 10) + (map[i + 2][j] - mindist);

                if (stidx[idx] == 0) {
                    stidx[idx] = stidx.size();
                }
                stlist[stidx[idx]].rc.emplace_back(i, j);
            }

            if (C - j > 2 && R - i > 1) { //type4
                mindist = map[i][j];
                mindist = min(mindist, map[i][j + 1]);
                mindist = min(mindist, map[i + 1][j + 1]);
                mindist = min(mindist, map[i + 1][j + 2]);

                idx = 400000 + ((map[i][j] - mindist) * 1000) + ((map[i][j + 1] - mindist) * 100) + ((map[i + 1][j + 1] - mindist) * 10) + map[i + 1][j + 2] - mindist;
                if (stidx[idx] == 0) {
                    stidx[idx] = stidx.size();
                }

                stlist[stidx[idx]].rc.emplace_back(i, j);
            }

            if (C - j > 2 && R - i > 2) { //type5
                mindist = map[i][j];
                mindist = min(mindist, map[i + 1][j]);
                mindist = min(mindist, map[i + 1][j + 1]);
                mindist = min(mindist, map[i + 1][j + 2]);
                mindist = min(mindist, map[i + 2][j + 2]);

                idx = 500000 + ((map[i][j] - mindist) * 10000) + ((map[i + 1][j] - mindist) * 1000) + ((map[i + 1][j + 1] - mindist) * 100) + ((map[i + 1][j + 2] - mindist) * 10) + map[i + 2][j + 2] - mindist;
                if (stidx[idx] == 0) {
                    stidx[idx] = stidx.size();
                }
                stlist[stidx[idx]].rc.emplace_back(i, j);
            }
        }
    }
}


void search(int type, int sidx) {
    for (int i = 0; i < stlist[stidx[sidx]].rc.size(); i++) {
        if (type == 1) {
            if (map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] != 9 && map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] != 9) {
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] = 9;
                answer = pair<int, int>(stlist[stidx[sidx]].rc.at(i).first, stlist[stidx[sidx]].rc.at(i).second);
                break;
            }
        }
        else if (type == 2) {
            if (map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] != 9 && map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] != 9 &&
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 2] != 9) {
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 2] = 9;
                answer = pair<int, int>(stlist[stidx[sidx]].rc.at(i).first, stlist[stidx[sidx]].rc.at(i).second);
                break;
            }
        }
        else if (type == 3) {
            if (map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] != 9 && map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second] != 9 &&
                map[stlist[stidx[sidx]].rc.at(i).first + 2][stlist[stidx[sidx]].rc.at(i).second] != 9) {
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 2][stlist[stidx[sidx]].rc.at(i).second] = 9;
                answer = pair<int, int>(stlist[stidx[sidx]].rc.at(i).first, stlist[stidx[sidx]].rc.at(i).second);
                break;
            }
        }
        else if (type == 4) {

            if (map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] != 9 && map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] != 9 &&
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 1] != 9 && map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 2] != 9) {
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second + 1] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 1] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 2] = 9;
                answer = pair<int, int>(stlist[stidx[sidx]].rc.at(i).first, stlist[stidx[sidx]].rc.at(i).second);
                break;
            }
        }
        else if (type == 5) {

            if (map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] != 9 && map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second] != 9 &&
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 1] != 9 && map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 2] != 9 &&
                map[stlist[stidx[sidx]].rc.at(i).first + 2][stlist[stidx[sidx]].rc.at(i).second + 2] != 9) {
                map[stlist[stidx[sidx]].rc.at(i).first][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 1] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 1][stlist[stidx[sidx]].rc.at(i).second + 2] = 9;
                map[stlist[stidx[sidx]].rc.at(i).first + 2][stlist[stidx[sidx]].rc.at(i).second + 2] = 9;
                answer = pair<int, int>(stlist[stidx[sidx]].rc.at(i).first, stlist[stidx[sidx]].rc.at(i).second);
                break;
            }
        }
    }
}


Result putPuzzle(int mPuzzle[3][3])
{
    Result ret = { -1, -1 };
    int mindist, pzidx;
    answer = pair<int, int>(-1, -1);
    vector<int>block;

    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (mPuzzle[i][j] != 0) {
                block.emplace_back(mPuzzle[i][j]);
            }
            if (block.size() == 1 && mPuzzle[i][j] != 0) {
                mindist = block.at(0);
            }
            else if (block.size() > 1 && mPuzzle[i][j] != 0) {
                mindist = min(mindist, block.at(block.size() - 1));
            }
        }
    }

    int type;
    if (mPuzzle[0][2] == 0 && mPuzzle[1][0] == 0 && mPuzzle[1][1] == 0 && mPuzzle[1][2] == 0) {
        type = 1;
        pzidx = 100000 + ((block.at(0) - mindist) * 10) + (block.at(1) - mindist);
    }
    else if (mPuzzle[0][2] != 0 && mPuzzle[1][0] == 0 && mPuzzle[1][1] == 0 && mPuzzle[1][2] == 0) {
        type = 2;
        pzidx = 200000 + ((block.at(0) - mindist) * 100) + ((block.at(1) - mindist) * 10) + (block.at(2) - mindist);
    }
    else if (mPuzzle[0][1] == 0 && mPuzzle[1][1] == 0 && mPuzzle[2][1] == 0) {
        type = 3;
        pzidx = 300000 + ((block.at(0) - mindist) * 100) + ((block.at(1) - mindist) * 10) + (block.at(2) - mindist);
    }
    else if (mPuzzle[0][2] == 0 && mPuzzle[1][0] == 0 && mPuzzle[1][1] != 0) {
        type = 4;
        pzidx = 400000 + ((block.at(0) - mindist) * 1000) + ((block.at(1) - mindist) * 100) + ((block.at(2) - mindist) * 10) + (block.at(3) - mindist);
    }
    else if (mPuzzle[0][1] == 0 && mPuzzle[0][2] == 0 && mPuzzle[1][1] != 0) {
        type = 5;
        pzidx = 500000 + ((block.at(0) - mindist) * 10000) + ((block.at(1) - mindist) * 1000) + ((block.at(2) - mindist) * 100) + ((block.at(3) - mindist) * 10) + (block.at(4) - mindist);
    }
    search(type, pzidx);
    ret = { answer.first, answer.second };
    return ret;
}

void clearPuzzles()
{
    for (int i = 0; i < R; i++) {
        for (int j = 0; j < C; j++) {
            map[i][j] = initmap[i][j];
        }
    }
    return;
}
```
