```cpp
/*
hash + vector
*/
#include<unordered_map>
#include<string>
#include<vector>
#include<algorithm>
using namespace std;

int cur;            // 현재 방 위치
struct Room {
    string word;    // 전체 단어
    string l;       // 앞 이동 단어
    string m;       // 중간 이동 단어
    string r;       // 뒤 이동 단어
}room[30005];

unordered_map<string, int> idmap;
unordered_map<string, vector<int>> pre, middle, post;

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
    pre[word.substr(0, 2)].push_back(mID);
    pre[word.substr(0, 4)].push_back(mID);

    // 중간 3자리
    middle[word.substr(4, 3)].push_back(mID);

    // 뒤 2자리, 4자리
    post[word.substr(9)].push_back(mID);
    post[word.substr(7)].push_back(mID);
}

void setCurrent(char mWord[]) {
    cur = idmap[mWord];
}

bool comp(int l, int r) { return room[l].word < room[r].word; }
int move(vector<int>& v) {
    if (v.empty()) return 0;
    else if (v.size() == 1) return cur != v[0] ? v[0] : 0;
    
    partial_sort(v.begin(), v.begin() + 2, v.end(), comp);
    return cur != v[0] ? v[0] : v[1];
}

int moveDir(int mDir) {
    int next;
    if (mDir == 0) next = move(post[room[cur].l]);      // 앞으로 이동
    if (mDir == 1) next = move(middle[room[cur].m]);    // 중간으로 이동
    if (mDir == 2) next = move(pre[room[cur].r]);       // 뒤로 이동

    if (next == 0) return 0;    // 이동할 곳 없는 경우
    return cur = next;
}

void erase(vector<int>& v, int x) {
    v.erase(find(v.begin(), v.end(), x));
}

void changeWord(char mWord[], char mChgWord[], int len[]) {
    int mid = idmap[mWord];

    // 모든 set에서 삭제
    string word = mWord;
    erase(pre[word.substr(0, 2)], mid);
    erase(pre[word.substr(0, 4)], mid);
    erase(middle[word.substr(4, 3)], mid);
    erase(post[word.substr(9)], mid);
    erase(post[word.substr(7)], mid);

    // 새롭게 등록
    addRoom(mid, mChgWord, len);
}

```
