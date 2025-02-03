```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

static unsigned int mSeed;
static unsigned int pseudo_rand(void)
{
    mSeed = mSeed * 214013 + 2531011;
    return (mSeed >> 16) & 0x7FFF;
}

struct Point
{
    int r;
    int c;
};

#define MAX_MAP_SIZE 2000
#define MAX_K 5

extern void init(int N, int K, int mHeight[][MAX_MAP_SIZE]);
extern void query(Point mA, Point mB, int mCount, Point mTop[]);
extern int getHeight(Point mP);
extern void work(Point mA, Point mB, int mH);
extern void destroy();
/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 100
#define CMD_QUERY 200
#define CMD_HEIGHT 300
#define CMD_WORK 400


static int map[MAX_MAP_SIZE][MAX_MAP_SIZE];
static Point result[MAX_K];

static void make_map(int n, int max_height, int seed)
{
    mSeed = seed;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            map[i][j] = 1 + ((pseudo_rand() << 15) + pseudo_rand()) % max_height;
        }
    }
}

static Point coordinates(int seed)
{
    Point p;
    p.r = seed / MAX_MAP_SIZE + 1;
    p.c = seed % MAX_MAP_SIZE + 1;
    return p;
}

static bool run()
{
    int n;
    scanf("%d", &n);

    int seed, size, K, max_height;
    Point a, b, c;
    int seed_a, seed_b, seed_c, d, k;
    int ans, ret;
    bool isOkay = false;

    for (int i = 0; i < n; ++i)
    {
        int cmd;
        scanf("%d", &cmd);

        switch (cmd)
        {
        case CMD_INIT:
            scanf("%d %d %d %d", &size, &K, &max_height, &seed);
            make_map(size, max_height, seed);
            init(size, K, map);
            isOkay = true;
            break;
        case CMD_QUERY:
            scanf("%d %d %d", &seed_a, &seed_b, &k);
            a = coordinates(seed_a);
            b = coordinates(seed_b);
            query(a, b, k, result);
            for (int j = 0; j < k; ++j)
            {
                scanf("%d", &seed_c);
                c = coordinates(seed_c);
                if (c.r != result[j].r || c.c != result[j].c)
                    isOkay = false;
            }
            break;
        case CMD_HEIGHT:
            scanf("%d", &seed_a);
            a = coordinates(seed_a);
            ret = getHeight(a);
            scanf("%d", &ans);
            if (ans != ret)
                isOkay = false;
            break;
        case CMD_WORK:
            scanf("%d %d %d", &seed_a, &seed_b, &d);
            a = coordinates(seed_a);
            b = coordinates(seed_b);
            work(a, b, d);
            break;
        default:
            isOkay = false;
            break;
        }
    }
    destroy();

    return isOkay;
}
#include <time.h>
int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("Preformance: %d ms\n", clock() - start);
    return 0;
}

```
