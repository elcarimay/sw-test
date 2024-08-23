```cpp
#if 1 // 18 ms
/*
* beat, delta hash table
* 8마디 정보 1개 정수로 저장
*/
int min(int a, int b) {
    return a < b ? a : b;
}
#define MAXL 7
struct Result {
    char mTitle[MAXL];
    int  mScore;
};
int strcmp(const char* s, const char* t) {
    while (*s && *s == *t) ++s, ++t;
    return *s - *t;
}
void strcpy(char* dest, const char* src) {
    while ((*dest++ = *src++));
}

int N;
const int SIZE = 1 << 18;
const int MASK = SIZE - 1;
const int scoreDict[8] = { 0, 10, 0, 0, 1, 50, 5, 100 }; // delta:4, melody:2, beat:1  [7]: delta,melody,beat가 다 일치할때 점수  [6]: delta, melody 일치할 때 점수 [0]:하나도 일치하지 않을때 점수

struct Track {
    char name[10];
    int data[3][103], len;  // 0: beat , 1:melody , 2:delta  . data[0][j] : beat[j~j+7] 구간을 정수로 합친 값
    int melody[103];        // melody 변경시 data[1] 업데이트를 편하게 하기 위해 입력 그대로 저장
}track[403];

struct Hash {
    int id, start;
    Hash* next;
    Hash* alloc(int _id, int _start, Hash* _next) {
        id = _id, start = _start, next = _next;
        return this;
    }
}hbuf[400 * 100 * 2], * btab[SIZE], * dtab[SIZE]; // beat, delta 해시 테이블
int hcnt;

void init() {
    N = hcnt = 0;
    for (int i = 0; i < SIZE; i++) btab[i] = dtab[i] = 0;
}

void insert(int x, int id, int start, Hash* htab[]) {
    int hidx = track[id].data[x][start] & MASK;
    htab[hidx] = hbuf[hcnt++].alloc(id, start, htab[hidx]);
}

int getBar(int data[], int minus = 0) { // 주어진 배열의 8개를 하나로 묶는다 data[0 ~ 7]의 값을 정수로 변환
    int x = 0;                          // delta의 경우 8개 값 중 가장 작은 값을 minus로 보내주어 최솟값을 0으로 만든다
    for (int i = 0; i < 8; i++) x = x * 10 + data[i] - minus;
    return x;
}

int getMin(int data[]) {                // delta를 위해 melody 8개 값 중 최솟값을 구하는 함수
    int min = 10;
    for (int i = 0; i < 8; i++) min = min > data[i] ? data[i] : min;
    return min;
}

void add(char name[7], int len, int melody[100], int beat[100]) {
    strcpy(track[++N].name, name);
    track[N].len = len;
    for (int i = 0; i < len; i++) {
        track[N].melody[i] = melody[i];
        if (i >= len - 7) continue;
        track[N].data[0][i] = getBar(beat + i);                         // beat     i~i+7 구간을 int로 변환 ex) 1,2,1,4,5,6,7,8 -> 12145678
        track[N].data[1][i] = getBar(melody + i);                       // melody
        track[N].data[2][i] = getBar(melody + i, getMin(melody + i));   // delta

        insert(0, N, i, btab);                                      // beat hash table에 추가
        insert(2, N, i, dtab);                                      // delta hash table에 추가
    }
}

int getID(char name[]) {    // track 수가 최대 400개 뿐이고 함수호출도 400번뿐이므로 linear search로 이름을 검색한다.
    for (int i = 1; i <= N; i++) {
        if (!strcmp(track[i].name, name)) return i;
    }
}

void erase(char name[7]) {
    int id = getID(name);
    track[id].len = 0;      // len을 0으로 해줌으로써 지워졌음을 표시한다.
}

void changePitch(char name[7], int delta) {
    int id = getID(name);
    for (int i = 0; i < track[id].len; i++) if (track[id].melody[i] + delta < 0 || track[id].melody[i] + delta > 9) return; // 범위 벗어나는 경우 바꾸지 않는다.
    for (int i = 0; i < track[id].len; i++) {   // melody[]와 data[1][]을 바꿔주고 melody hash table에 추가한다.
        track[id].melody[i] += delta;           // data[2][] 는 바뀌지 않는다 (변화폭은 그대로이므로)
        if (i < 7) continue;
        track[id].data[1][i - 7] = getBar(track[id].melody + i - 7);
    }
}

void probing(int data[], Hash* htab[], int x, int score[]) {
    int hidx = data[x] & MASK;
    for (Hash* p = htab[hidx]; p; p = p->next) {
        if (track[p->id].len && track[p->id].data[x][p->start] == data[x]) {   // len이 0이면 삭제된 경우
            int c = 0;
            for (int i = 0; i <= 2; i++)
                if (track[p->id].data[i][p->start] == data[i]) c |= 1 << i; //  2^0:beat , 2^1:melody , 2^2:delta. 
            if (x == 2 || c == 1)
                score[p->id] += scoreDict[c]; // x가 delta인 경우 score계산 or c가 1인 경우: beat만 일치한 경우 socre 계산
        }
    }
}

Result getSimilarity(int len, int melody[100], int beat[100]) {
    Result re = { "", 0 };
    int resultScore = -1;
    int score[403] = {};
    for (int i = 0; i < len - 7; i++) {
        int data[3] = { getBar(beat + i), getBar(melody + i), getBar(melody + i, getMin(melody + i)) };
        probing(data, dtab, 2, score);  // delta가 일치하는 점수 계산 (data[2]가 delta)
        probing(data, btab, 0, score);  // beat가 일치하면서 melody, delta가 일치하지 않는 점수 계산 (data[0]이 beat)
    }
    for (int i = 1; i <= N; i++) {   // score 최댓값 구하기
        if (track[i].len && score[i] > resultScore) {    // len이 0이면 삭제된 경우
            resultScore = score[i];
            strcpy(re.mTitle, track[i].name);
        }
    }
    re.mScore = resultScore;
    return re;
}
#endif // 1
```
