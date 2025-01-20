```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include<string>
#include<unordered_map>
#include<unordered_set>
#include<algorithm>
#include<string.h>
#include<vector>
using namespace std;
using ull = unsigned long long;

int n, m, dictCnt;
int wordID[40003];

char S[10003][13];
char dictS[1003][13];
unordered_set<ull> dict;
unordered_map<ull, int> dict2;
vector<char*> v[10003];

ull getHash(char* str) {
    ull hash = 0;
    for (int i = 0; str[i]; i++) {      // hornor's method
        hash = hash * 28;
        if (str[i] == '*') hash += 27;
        else hash += str[i] - 96;       // 'a':1 , 'z':26
    }
    return hash;
}

void init(int N, char str[])
{
    int cnt = 0;
    n = m = dictCnt = 0;
    dict.clear();
    dict2.clear();
    int len = 0;
    char delim[] = "_";
    char* p = strtok(str, delim);
    int i = strlen(p) + 1;
    wordID[i] = n + 1;
    strcpy(S[n++], p);
    while (p) {
        p = strtok(nullptr, delim);
        wordID[i += strlen(p) + 1] = n + 1;
        strcpy(S[n++], p);
    }
    /*str[N] = '_';
    for (int i = 0; i <= N; i++) {
        if (str[i] == '_') {
            S[n][len] = 0;
            len = 0;
            wordID[i + 1] = ++n;
        }
        else S[n][len++] = str[i];
    }*/
}

int getId(char* str, ull hash = 0) {
    if (hash == 0) hash = getHash(str);
    if (dict2.count(hash)) return dict2[hash];
    v[m].clear();
    return dict2[hash] = m++;
}

void addWord(char word[])
{
    strcpy(dictS[++dictCnt], word);
    ull hash = getHash(word);
    dict.insert(hash);
    char str[13];
    strcpy(str, word);
    for (int i = 0; word[i]; i++) {
        str[i] = '*';
        int id = getId(str);
        v[id].push_back(dictS[dictCnt]);
        str[i] = word[i];
    }
}

void removeWord(char word[])
{
    char str[13];
    strcpy(str, word);
    ull hash = getHash(str);
    dict.erase(hash);
    for (int i = 0; word[i]; i++) {
        str[i] = '*';
        ull hash = getHash(str);
        int id = getId(str, hash);
        v[id].erase(find_if(v[id].begin(), v[id].end(), [&](auto x) {return !strcmp(x, word); }));
        if (v[id].empty()) dict2.erase(hash);
        str[i] = word[i];
    }
}

int correct(int start, int end)
{
    char str[13];
    int cnt = 0;
    for (int i = wordID[start]; i < n && i < wordID[end + 2]; i++) {
        ull hash = getHash(S[i]);
        if (dict.count(hash)) continue;

        strcpy(str, S[i]);
        char ret[13] = { 'z' + 1 };
        for (int j = 0; str[j]; j++) {
            str[j] = '*';
            hash = getHash(str);
            if (dict2.count(hash)) {
                int id = dict2[hash];

                auto it = min_element(v[id].begin(), v[id].end(), [&](auto l, auto r) { return strcmp(l, r) < 0; });
                if (strcmp(ret, *it) > 0) strcpy(ret, *it);
            }
            str[j] = S[i][j];
        }
        if (ret[0] != 'z' + 1) {
            strcpy(S[i], ret);
            cnt++;
        }
    }
    return cnt;
}

void destroy(char result[])
{
    int len = 0;
    for (int i = 0; i < n; i++) {
        for (int j = 0; S[i][j]; j++) result[len++] = S[i][j];
        result[len++] = '_';
    }
    result[len - 1] = 0;
}
#endif // 1

```
