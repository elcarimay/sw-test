```python
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N);
extern int apple(int r, int c, int len);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

static bool run()
{
    bool correct = true;
    int N;
    scanf("%d", &N);
    init(N);
    printf("%d\n", N);

    int queryCnt;
    scanf("%d", &queryCnt);
    printf("%d\n", queryCnt);

    int r, c, len;

    for (int i = 0; i < queryCnt; i++)
    {
        scanf("%d %d %d", &r, &c, &len);
        int lenRes = 0;
        if (correct)
        {
            lenRes = apple(r, c, len);
        }

        int lenAns;
        scanf("%d", &lenAns);
        if (lenAns != lenRes)
        {
            correct = false;
        }
    }
    return correct;
}

int main()
{
    setbuf(stdout, NULL);
    freopen("sample_input_210409_용게임.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    return 0;
}

#define UP    0
#define RIGHT 1
#define DOWN  2
#define LEFT  3

struct Pos {
    int r, c;
};

int dr[] = { -1,0,1,0 };
int dc[] = { 0,1,0,-1 };


struct Dragon {
    Pos body[500000];
    int wp, rp;
    int dir;

    void move(int mDir, int cnt, bool apple) {

        for (int i = 0; i < cnt; i++) {
            Pos pos;
            pos.r = body[wp - 1].r + dr[mDir];
            pos.c = body[wp - 1].c + dc[mDir];
            body[wp++] = pos;

            if (!apple)
                ++rp;

            for (int i = wp - 2; i >= rp; i--) {
                if (body[i].r == body[wp - 1].r && body[i].c == body[wp - 1].c) {
                    rp = i + 1;
                    break;
                }
            }
        }

        dir = mDir;
    }

    Pos getHead() {
        return body[wp - 1];
    }

    int getDragonLength() {
        return wp - rp;
    }
};

Dragon dragon;
int abs(int a) { return a < 0 ? -a : a; }

void init(int N)
{
    dragon.rp = 0;
    dragon.wp = 1;
    dragon.body[0].r = (N + 1) / 2;
    dragon.body[0].c = (N + 1) / 2;
    dragon.dir = DOWN;
}

int apple(int r, int c, int len)
{
    Pos head = dragon.getHead();
    int dist_r = abs(head.r - r);
    int dist_c = abs(head.c - c);

    if (r < head.r && c < head.c) { // 사과의 위치 : 왼쪽 위
        if (dragon.dir == UP || dragon.dir == RIGHT) {
            dragon.move(UP, dist_r, false);
            dragon.move(LEFT, dist_c, false);
            dragon.move(LEFT, len, true);
        }
        else {
            dragon.move(LEFT, dist_c, false);
            dragon.move(UP, dist_r, false);
            dragon.move(UP, len, true);
        }
    }
    else if (r < head.r && c > head.c) {// 사과의 위치 : 오른쪽 위
        if (dragon.dir == UP || dragon.dir == LEFT) {
            dragon.move(UP, dist_r, false);
            dragon.move(RIGHT, dist_c, false);
            dragon.move(RIGHT, len, true);
        }
        else {
            dragon.move(RIGHT, dist_c, false);
            dragon.move(UP, dist_r, false);
            dragon.move(UP, len, true);
        }

    }
    else if (r > head.r && c < head.c) {// 사과의 위치 : 왼쪽 아래
        if (dragon.dir == UP || dragon.dir == LEFT) {
            dragon.move(LEFT, dist_c, false);
            dragon.move(DOWN, dist_r, false);
            dragon.move(DOWN, len, true);
        }
        else {
            dragon.move(DOWN, dist_r, false);
            dragon.move(LEFT, dist_c, false);
            dragon.move(LEFT, len, true);
        }

    }
    else {// 사과의 위치 : 오른쪽 아래
        if (dragon.dir == UP || dragon.dir == RIGHT) {
            dragon.move(RIGHT, dist_c, false);
            dragon.move(DOWN, dist_r, false);
            dragon.move(DOWN, len, true);
        }
        else {
            dragon.move(DOWN, dist_r, false);
            int dLength = dragon.getDragonLength();

            dragon.move(RIGHT, dist_c, false);
            dLength = dragon.getDragonLength();


            dragon.move(RIGHT, len, true);
        }
    }

    head = dragon.getHead();
    int dLength = dragon.getDragonLength();
    return dLength;
}

```
