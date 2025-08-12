#define MAXL (10)

// ===== 내부 구현: Trie =====
struct Node {
    int child[26];
    int cnt;      // 서브트리 단어 수 (자기 자신 포함: isWord==1이면)
    char isWord;  // 0/1
};

#define MAXNODES 300000
static Node T[MAXNODES];
static int NODE_CNT;   // 사용 중인 노드 수
static int PAGE_SIZE;  // 한 페이지 단어 수
char mRet[MAXL + 1];   // 요구된 반환 버퍼 (이미 주어짐)

// 노드 초기화
static inline void init_node(int i){
    T[i].cnt = 0;
    T[i].isWord = 0;
    for (int k=0; k<26; ++k) T[i].child[k] = -1;
}

// 전체 초기화
static inline void reset(int pageSize){
    PAGE_SIZE = pageSize;
    NODE_CNT = 1;      // 0번이 루트
    init_node(0);
}

// 문자 → 인덱스
static inline int cidx(char c){ return c - 'a'; }

// 단어 1개 추가 (중복 없음 보장)
static inline void addOne(const char s[]){
    int u = 0;
    T[u].cnt++;
    for (int i=0; s[i]; ++i){
        int c = cidx(s[i]);
        if (T[u].child[c] == -1){
            T[u].child[c] = NODE_CNT;
            init_node(NODE_CNT);
            NODE_CNT++;
        }
        u = T[u].child[c];
        T[u].cnt++;
    }
    T[u].isWord = 1;
}

// 단어 1개 삭제 (존재 보장)
static inline void removeOne(const char s[]){
    int u = 0;
    T[u].cnt--;
    for (int i=0; s[i]; ++i){
        int c = cidx(s[i]);
        u = T[u].child[c];
        T[u].cnt--;
    }
    T[u].isWord = 0;
}

// 사전 내 1-based 순위
static inline int rankOf(const char s[]){
    int u = 0;
    int rank = 0;
    for (int i=0; s[i]; ++i){
        int c = cidx(s[i]);
        // 현재 자리에서 c보다 작은 모든 자식의 cnt 합
        for (int d=0; d<c; ++d){
            int v = T[u].child[d];
            if (v != -1) rank += T[v].cnt;
        }
        u = T[u].child[c]; // 존재 보장
        if (s[i+1] == '\0'){
            // 여기서 끝나는 단어가 있으면 그 단어를 포함
            if (T[u].isWord) rank += 1;
            return rank;
        }
    }
    return rank; // 보장상 도달
}

// 1-based k번째 단어를 mRet에 작성 후 포인터 반환
static inline char* kth(int k){
    int len = 0;
    int u = 0;
    while (1){
        if (T[u].isWord){
            if (k == 1){
                mRet[len] = '\0';
                return mRet;
            }
            --k;
        }
        // 자식들을 'a'..'z' 순서로 탐색
        for (int d=0; d<26; ++d){
            int v = T[u].child[d];
            if (v == -1) continue;
            int c = T[v].cnt;
            if (k > c){
                k -= c;
            } else {
                mRet[len++] = (char)('a' + d);
                u = v;
                break;
            }
        }
    }
}

// ===== 필수 API 구현 =====

void init(int N, char mWordList[][MAXL+1], int mWordSize)
{
    reset(N);
    for (int i=0; i<mWordSize; ++i){
        addOne(mWordList[i]);
    }
}

void addWord(char mWordList[][MAXL+1], int mWordSize)
{
    for (int i=0; i<mWordSize; ++i){
        addOne(mWordList[i]);
    }
}

void removeWord(char mWordList[][MAXL + 1], int mWordSize)
{
    for (int i=0; i<mWordSize; ++i){
        removeOne(mWordList[i]);
    }
}

char* findWord(int mPageNum)
{
    // mPageNum 페이지의 첫 단어 = 전역 순위 (mPageNum-1)*PAGE_SIZE + 1
    int k = (mPageNum - 1) * PAGE_SIZE + 1;
    return kth(k);
}

int findPage(char mWord[])
{
    int r = rankOf(mWord);            // 1-based rank
    return (r - 1) / PAGE_SIZE + 1;   // 페이지 번호
}