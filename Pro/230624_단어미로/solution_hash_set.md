```cpp
/*
hash + set
*/
#include<unordered_map>
#include<set>
#include<string>
using namespace std;

int cur;            // 현재 방 위치
struct Room {
    string word;    // 전체 단어
    string l;       // 앞 이동 단어
    string m;       // 중간 이동 단어
    string r;       // 뒤 이동 단어
}room[30005];

struct comp {
    bool operator()(const int l, const int r) const {
        return room[l].word < room[r].word;
    }
};
unordered_map<string, int> idmap;
unordered_map<string, set<int, comp>> pre, middle, post;

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
    pre[word.substr(0, 2)].insert(mID);
    pre[word.substr(0, 4)].insert(mID);

    // 중간 3자리
    middle[word.substr(4, 3)].insert(mID);

    // 뒤 2자리, 4자리
    post[word.substr(9)].insert(mID);
    post[word.substr(7)].insert(mID);
}

void setCurrent(char mWord[]) {
    cur = idmap[mWord];
}

int move(set<int, comp>& s) {
    // 최우선순위 방 선택
    auto it = s.begin();

    // 최우선순위가 현재 방인 경우 다음 방 선택
    if (it != s.end() && *it == cur) ++it;

    // 이동하는 방 번호 반환(없는 경우 0)
    return it != s.end() ? *it : 0;
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

    // 모든 set에서 삭제
    string word = mWord;
    pre[word.substr(0, 2)].erase(mid);
    pre[word.substr(0, 4)].erase(mid);
    middle[word.substr(4, 3)].erase(mid);
    post[word.substr(9)].erase(mid);
    post[word.substr(7)].erase(mid);

    // 새롭게 등록
    addRoom(mid, mChgWord, len);
}

```
