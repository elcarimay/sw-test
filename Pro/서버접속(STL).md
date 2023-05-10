```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <string>
#include <unordered_map>
#include <vector>

using namespace std;

struct User {
	string password;		// 입력값
	int defaultTime;		// 입력값

	bool isLogin;
	int logoutTime;			// 로그아웃 되는 시간 = currentTime + defaultTime
};

unordered_map<string, int> mapID;
vector<User> users;

int currentTime;			// current timestamp
unordered_map<int, vector<int>> logoutUsers;

/////////////////////////////////////////////////////////////
void Init()
{
	mapID.clear();
	users.clear();
	currentTime = 0;

	logoutUsers.clear();
}

void NewAccount(char id[11], char password[11], int defaulttime)
{
	int userID = users.size();
	mapID[string(id)] = userID;

	users.push_back(User());
	users[userID].password = string(password);
	users[userID].defaultTime = defaulttime;
	users[userID].logoutTime = currentTime + users[userID].defaultTime;
	users[userID].isLogin = true;

	logoutUsers[users[userID].logoutTime].push_back(userID);
}

void Logout(char id[11])
{
	int userID = mapID[string(id)];

	if (users[userID].isLogin)
		users[userID].isLogin = false;
}

void Connect(char id[11], char password[11])
{
	int userID = mapID[string(id)];

	if (users[userID].isLogin && users[userID].password == string(password)) {
		users[userID].logoutTime = currentTime + users[userID].defaultTime;
		logoutUsers[users[userID].logoutTime].push_back(userID);
	}
}

int Tick()
{
	int logoutUserCnt = 0;
	currentTime += 1;

	for (auto uID : logoutUsers[currentTime]) {
		if (users[uID].isLogin && users[uID].logoutTime == currentTime) {
			users[uID].isLogin = false;
			logoutUserCnt++;
		}
	}
	return logoutUserCnt;
}

```
