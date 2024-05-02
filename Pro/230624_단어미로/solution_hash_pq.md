```cpp
/*
hash + pq
*/
#include<unordered_map>
#include<queue>
#include<string>
using namespace std;
using psi = pair<string, int>;

int cur;            // 현재 방 위치
struct Room {
    string word;    // 전체 단어
    string l;       // 앞 이동 단어
    string m;       // 중간 이동 단어
    string r;       // 뒤 이동 단어
}room[30005];

unordered_map<string, int> idmap;
unordered_map<string, priority_queue<psi, vector<psi>, greater<>>> pre, middle, post;

void init() {
    idmap.clear();
    pre.clear();
    middle.clear();
    post.clear();
}

void addRoom(int mID, char mWord[], int len[]) {
    string word = mWord;
    idmap[mWord] = mID;
    room[mID] = { word, word.substr(0, len[0]), word.substr(4, 3), word.substr(11 - len[2]) };

    // 앞 2자리, 4자리
    pre[word.substr(0, 2)].push({ word, mID });
    pre[word.substr(0, 4)].push({ word, mID });

    // 중간 3자리
    middle[word.substr(4, 3)].push({ word, mID });

    // 뒤 2자리, 4자리
    post[word.substr(9)].push({ word, mID });
    post[word.substr(7)].push({ word, mID });
}

void setCurrent(char mWord[]) {
    cur = idmap[mWord];
}

int move(priority_queue<psi, vector<psi>, greater<>>& pq) {
    int next = 0;
    bool flag = 0;
    while (pq.size()) {
        const string&word= pq.top().first;
        int mid = pq.top().second;

        if (word != room[mid].word) {
            pq.pop();
            continue;
        }

        if (cur == mid) {
            flag = 1;
            pq.pop();
            continue;
        }

        next = mid;
        break;
    }
    if (flag) pq.push({ room[cur].word, cur });
    return next;
}

int moveDir(int mDir) {
    int next;
    if (mDir == 0) next = move(post[room[cur].l]);      // 앞으로 이동
    if (mDir == 1) next = move(middle[room[cur].m]);    // 중간으로 이동
    if (mDir == 2) next = move(pre[room[cur].r]);       // 뒤로 이동

    if (next == 0) return 0;    // 이동할 곳 없는 경우
    return cur = next;
}

void changeWord(char mWord[], char mChgWord[], int len[]) {
    int mid = idmap[mWord];
    addRoom(mid, mChgWord, len);
}
```
