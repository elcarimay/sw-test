```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <string>
#include <unordered_map>
#include <vector>

using namespace std;

#define MAX_USER 50000
#define MAX_TIME 100000 // 8만까지 하면 되나 넉넉히 잡음.
#define MAX_TABLE 50007 // 서로 다른 원소의 숫자와 비슷한 숫자, hash함수 특성상 3,7,9같은 숫자를 넣음

struct User {
    string password;
    bool isLogout;
    int defaulttime;
    int logouttime; // 로그아웃되는 시간 = currenttime + defaulttime
};

unordered_map<string, int> mapID;
vector<User> user;

int currenttime;
unordered_map<int, vector<int>> logoutUsers;

void Init()
{
    mapID.clear();
    user.clear();
    currenttime = 0;

    logoutUsers.clear();
}

void NewAccount(char id[11], char password[11], int defaulttime)
{ // 중괄호내에 변수를 만들면 stack 영역에 저장되므로 함수호출이 끝나면 사라짐.(지역변수)
    int userCount = user.size();
    mapID[string(id)] = userCount;
    user.push_back(User());

    user[userCount].password = string(password);
    user[userCount].defaulttime = defaulttime;
    user[userCount].logouttime = currenttime + defaulttime;
    user[userCount].isLogout = false;

    logoutUsers[user[userCount].logouttime].push_back(userCount);
}

void Logout(char id[11])
{
    int uIdx = mapID[string(id)];
    if (!user[uIdx].isLogout)
        user[uIdx].isLogout = true;
}

void Connect(char id[11], char password[11])
{
    int uIdx = mapID[string(id)];
    if (user[uIdx].isLogout) return;
    if (user[uIdx].password == string(password)) {
        user[uIdx].logouttime = currenttime + user[uIdx].defaulttime;
        logoutUsers[user[uIdx].logouttime].push_back(uIdx);
    }
}


int Tick()
{
    int logoutUserCount = 0;
    currenttime++;

    for (auto uID : logoutUsers[currenttime]) {
        if (!user[uID].isLogout && user[uID].logouttime == currenttime) {
            user[uID].isLogout = true;
            logoutUserCount++;
        }
    }
    return logoutUserCount;
}

// 실행시간: 약  40 ms
// 실행시간: 약  40 ms
```
