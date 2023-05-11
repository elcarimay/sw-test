```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <string.h>

#define MAX_USER 50000

struct User {
    char id[11];
    char password[11];
    bool isLogout;
    int defaulttime;
    int logintime; // 로그인 상태를 유지할 수 있는 시간
};

User user[MAX_USER];
int userCount;

void Init()
{
    userCount = 0;
}

void NewAccount(char id[11], char password[11], int defaulttime)
{ // 중괄호내에 변수를 만들면 stack 영역에 저장되므로 함수호출이 끝나면 사라짐.(지역변수)
    strcpy(user[userCount].id, id);
    strcpy(user[userCount].password, password);
    user[userCount].defaulttime = defaulttime;
    user[userCount].logintime = defaulttime;
    user[userCount].isLogout = false;
    userCount++; // 현재 사용자가 몇명인지 나타내고 새로운 사용자가 저장될 Index위치임.
}

int getUserIdx(char id[11])
{
    for (int i = 0;i<userCount;i++)
        if(strcmp(user[i].id,id) == 0) return i;
    return -1;
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
    user[uIdx].logintime = user[uIdx].defaulttime;
}


int Tick()
{
    int logoutUserCount = 0;
    for(int i = 0;i < userCount;i++)
    {
        if(user[i].isLogout) continue;
        user[i].logintime--;
        if(user[i].logintime == 0)
        {
            user[i].isLogout = true;
            logoutUserCount++;
        }
            
    }
    return logoutUserCount;
}

// 실행시간: 약 650 ms
```
