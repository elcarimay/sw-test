[문제 설명]
한 페이지에 N 개의 단어들이 있는 단어사전이 있다.
단어는 길이 10 이하의 영어 소문자로 구성된 문자열이다.
단어들은 사전순으로 정렬 되어 있다.
만약 단어가 10개 존재하고 N 이 3 이라면 첫번째 페이지에는 사전순으로 1번째 ~ 3번째인 단어들이 존재하고, 두번째 페이지에는 4번째 ~ 6번째인 단어들, 마지막 페이지인 4번째 페이지에는 사전순으로 10번째인 단어 1개만이 존재한다.
단어들은 새로 추가되기도 하고, 사전에 존재하는 단어들이 없어지기도 한다.
이런 경우에도 단어들은 사전순으로 관리되어야 한다.
또한 특정 페이지의 첫 번째에 존재하는 단어를 찾아야 한다.
만약 위의 경우 두 번째 페이지에 존재하는 단어를 찾아야 한다면, 사전순으로 4번째에 해당하는 단어를 반환하면 된다.
마지막으로 특정 단어가 주어졌을 때, 그 단어가 존재하는 페이지 번호를 반환해야 한다.
아래 API 설명을 참조하여 각 함수를 구현하라.
아래는 User Code 부분에 작성해야 하는 API 의 설명이다.

void init(int N, string mWordList[], int mWordSize)
테스트 케이스에 대한 초기화 함수.
각 테스트 케이스의 맨 처음 1회 호출된다.
N 은 한 페이지에 존재하는 단어의 갯수를 의미한다.
mWordList 는 초기에 존재하는 단어들이다.
단어들은 중복 없이 주어진다.
주어지는 단어들은 사전순으로 정렬되어 있지 않음에 유의하라.
mWordSize 는 초기에 존재하는 단어의 갯수이다.
string type 은 각 언어별로 다르게 주어지므로, 주어진 템플릿 코드를 기준으로 작성하라.
Parameters
N : 한 페이지에 존재하는 단어의 수 (1 ≤ N ≤ 10,000)
  mWordList : 초기에 존재하는 단어들 (1 ≤ 각 단어의 길이 ≤ 10)
  mWordSize : 초기에 존재하는 단어의 갯수 (5 ≤ mWordSize ≤ 10,000)

void addWord(string mWordList[], int mWordSize)
새로운 단어들이 추가 된다.
mWordSize는 추가할 단어의 갯수이다.
이 단어들은 사전에 존재하지 않는 것들이고, 중복되지 않게 주어진다.
주어지는 단어들은 사전순으로 정렬되어 있지 않음에 유의하라.
Parameters
  mWordList : 추가할 단어들 (1 ≤ 각 단어의 길이 ≤ 10)
  mWordSize : 추가할 단어의 갯수 (1 ≤ mWordSize ≤ 100)

void removeWord(string mWordList[], int mWordSize)
단어들이 사전에서 삭제 된다.
mWordSize는 삭제할 단어의 갯수이다.
삭제할 단어들은 사전에 존재하는 것들이고, 중복되지 않게 주어진다.
Parameters
  mWordList : 삭제할 단어들 (1 ≤ 각 단어의 길이 ≤ 10)
  mWordSize : 삭제할 단어의 갯수 (1 ≤ mWordSize ≤ 100)

string findWord(int mPageNum)
사전에서 mPageNum 페이지의 첫번째 단어를 찾아 반환한다.
mPageNum은 현재 존재하는 페이지 번호임이 보장된다.
Parameters
  mPageNum : 단어를 찾을 페이지
Return
  해당 페이지의 첫번째 단어

int findPage(string mWord)
사전에서 mWord 가 존재하는 페이지 번호를 반환한다.
단어는 현재 사전에 존재하는 것만 주어진다.
Parameters
  mWord : 페이지를 찾을 단어 (1 ≤ 단어의 길이 ≤ 10)
Return
  해당 단어가 존재하는 페이지 번호

// Dictionary with paging — C++11, standard headers only
#include <string>
#include <vector>
#include <deque>
#include <queue>
#include <unordered_map>
#include <algorithm>
#include <utility>
#include <cstring>

using namespace std;

namespace Dict {

struct Node {
    int child[26];
    int cnt;      // subtree words (include self if isWord)
    bool isWord;
    Node() : cnt(0), isWord(false) { memset(child, -1, sizeof(child)); }
};

static vector<Node> T;
static int PAGE = 1;

inline int idx(char c){ return c - 'a'; }

void reset(int pageSize){
    PAGE = pageSize;
    T.clear(); T.reserve(200000);
    T.push_back(Node()); // root = 0
}

void addOne(const string& s){
    int u = 0;
    T[u].cnt++;
    for(char ch : s){
        int c = idx(ch);
        if(T[u].child[c] == -1){
            T[u].child[c] = (int)T.size();
            T.push_back(Node());
        }
        u = T[u].child[c];
        T[u].cnt++;
    }
    T[u].isWord = true;
}

// s 가 존재한다고 보장
void removeOne(const string& s){
    int u = 0;
    T[u].cnt--;
    for(char ch : s){
        int c = idx(ch);
        int v = T[u].child[c];
        u = v;
        T[u].cnt--;
    }
    T[u].isWord = false;
}

// 1-based rank of s
int rankOf(const string& s){
    int u = 0;
    int rank = 0;
    for(size_t i=0;i<s.size();++i){
        int c = idx(s[i]);
        // add counts of children < c
        for(int d=0; d<c; ++d){
            int v = T[u].child[d];
            if(v!=-1) rank += T[v].cnt;
        }
        u = T[u].child[c];
        if(u==-1) return rank; // 주어진 문제 조건상 발생하지 않음
        if(i == s.size()-1){
            rank += (T[u].isWord ? 1 : 0);
            return rank;
        }
    }
    return rank;
}

// find k-th word (1-based). assumes 1<=k<=T[0].cnt
string kth(int k){
    string res;
    int u = 0;
    while(true){
        if(T[u].isWord){
            if(k==1) return res;
            --k;
        }
        for(int d=0; d<26; ++d){
            int v = T[u].child[d];
            if(v==-1) continue;
            int c = T[v].cnt;
            if(k > c){
                k -= c;
            }else{
                res.push_back('a'+d);
                u = v;
                break;
            }
        }
    }
}

} // namespace Dict

// ========== User API ==========

void init(int N, std::string mWordList[], int mWordSize){
    Dict::reset(N);
    for(int i=0;i<mWordSize;++i){
        Dict::addOne(mWordList[i]);
    }
}

void addWord(std::string mWordList[], int mWordSize){
    for(int i=0;i<mWordSize;++i){
        Dict::addOne(mWordList[i]);
    }
}

void removeWord(std::string mWordList[], int mWordSize){
    for(int i=0;i<mWordSize;++i){
        Dict::removeOne(mWordList[i]);
    }
}

std::string findWord(int mPageNum){
    int k = (mPageNum-1)*Dict::PAGE + 1; // 페이지 첫 단어의 전역 순위
    return Dict::kth(k);
}

int findPage(std::string mWord){
    int r = Dict::rankOf(mWord); // 1-based
    return (r-1)/Dict::PAGE + 1;
}