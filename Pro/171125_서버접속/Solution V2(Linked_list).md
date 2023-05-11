```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <string.h>

#define MAX_USER 50000
#define MAX_TIME 100000 // 8만까지 하면 되나 넉넉히 잡음.
#define MAX_TABLE 50007 // 서로 다른 원소의 숫자와 비슷한 숫자, hash함수 특성상 3,7,9같은 숫자를 넣음

struct ListNode{
    int id; // user배열의 인덱스 정보
    ListNode* next;
};

ListNode heap[1000000];
int hrp;

// 새로운 노드를 만들고 그 새로운 노드가 기존 head를 가르키게 하고 새로만들어진 노드의 주소를 리턴
ListNode* appendListNode(int id, ListNode* oldhead){
    //ListNode* node = new ListNode;
    ListNode* node = &heap[hrp++];
    node->id = id;
    node->next = oldhead;
    return node;
}

ListNode* head[MAX_TABLE];
ListNode* logoutUser[MAX_TIME];

// 그냥 복사해서 사용하고 내부로직 알필요 없음.
unsigned long hash( const char *str)
{
     unsigned long hash = 5381;
     int c;
 
     while (c = *str++)
     {
         hash = (((hash << 5) + hash) + c) % MAX_TABLE;
     }
 
     return hash % MAX_TABLE;
}

struct User {
    char id[11];
    char password[11];
    bool isLogout;
    int defaulttime;
    // int logintime; // 로그인 상태를 유지할 수 있는 시간
    int logouttime;
};

User user[MAX_USER];

int userCount;
int currenttime;

void Init()
{
    hrp = 0;
    userCount = 0;
    currenttime = 0;
    for (int i = 0; i < MAX_TABLE; i++)
    {
        head[i] = 0;
    }
    for (int i = 0; i < MAX_TIME; i++)
    {
        logoutUser[i] = 0;
    }
    
}

void NewAccount(char id[11], char password[11], int defaulttime)
{ // 중괄호내에 변수를 만들면 stack 영역에 저장되므로 함수호출이 끝나면 사라짐.(지역변수)
    strcpy(user[userCount].id, id);
    strcpy(user[userCount].password, password);
    user[userCount].defaulttime = defaulttime;
    user[userCount].logouttime = currenttime + defaulttime;
    user[userCount].isLogout = false;

    int hashkey = hash(id);
    head[hashkey] = appendListNode(userCount, head[hashkey]);
    // 기존 head에 node를 추가하여 신규 노드를 반환.
    
    logoutUser[user[userCount].logouttime] = appendListNode(userCount, 
                                        logoutUser[user[userCount].logouttime]);
    userCount++; // 현재 사용자가 몇명인지 나타내고 새로운 사용자가 저장될 Index위치임.
}

int getUserIdx(char id[11])
{
    int hashkey = hash(id);
    ListNode* temp = head[hashkey];
    while(temp!=0){ // 부분탐색(개선포인트 1번째) -> 실행시간: 약 500 ms
        int i = temp->id;
        if(strcmp(user[i].id, id)==0)
            return i;
        temp = temp->next;
    }

    // 전체탐색
    // for (int i = 0;i<userCount;i++)
    //     if(strcmp(user[i].id,id) == 0) return i;
    // return -1;
}


void Logout(char id[11])
{
    int uIdx = getUserIdx(id);
    if (uIdx == -1) return;
    if (user[uIdx].isLogout) return;
    user[uIdx].isLogout = true;
}

void Connect(char id[11], char password[11])
{
    int uIdx = getUserIdx(id);
    if (uIdx == -1) return;
    if (user[uIdx].isLogout) return;
    if (strcmp(user[uIdx].password,password)!=0) return;
    user[uIdx].logouttime = currenttime + user[uIdx].defaulttime;
    logoutUser[user[uIdx].logouttime] = appendListNode(uIdx, 
                                        logoutUser[user[uIdx].logouttime]); // 배열크기 주의
}


int Tick() // 반드시 Linked list사용 권장(개선포인트 2번째) -> 실행시간: 약 28 ms
{
    int logoutUserCount = 0;
    currenttime++;

    ListNode* temp = logoutUser[currenttime];

    while(temp!=0){
        int i = temp->id;

        if(!user[i].isLogout && user[i].logouttime == currenttime){
            user[i].isLogout = true;
            logoutUserCount++;
        }
        temp = temp->next;

    }
    // 전체탐색
    // for(int i = 0;i < userCount;i++)
    // {
    //     if(user[i].isLogout) continue;
    //     if(user[i].logouttime == currenttime)
    //     {
    //         user[i].isLogout = true;
    //         logoutUserCount++;
    //     }
            
    // }
    return logoutUserCount;
}
```
