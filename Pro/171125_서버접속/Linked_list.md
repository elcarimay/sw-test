```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>
#include <time.h>

#define MAX_ACCOUNT 50000
#define MAX_TIME	100000
#define MAX_USER	50000
#define MAX_TABLE	50007

struct User {
	char id[11];
	char password[11];
	bool isLogout;
	int defaulttime;
	int logintime;  // 로그인상태를 유지할 수 있는 시간
	int logouttime;
};

struct ListNode {
	int id; // user배열의 인덱스 정보
	ListNode* next;
};

ListNode heap[1000000];
int hrp;

// 새로운 노드를 만들고 그 새로운 노드가 기존 head를 가르키게 하고 새로만들어진 노드의 주소를 리턴한다.
ListNode* appendListNode(int id, ListNode* oldhead) {
	//ListNode* node = new ListNode;
	ListNode* node = &heap[hrp++];
	node->id = id;
	node->next = oldhead;
	return node;
}

ListNode* head[MAX_TABLE];
ListNode* logoutUser[MAX_TIME];

unsigned long hash(const char* str)
{
	unsigned long hash = 5381;
	int c;
	while (c = *str++)
	{
		hash = (((hash << 5) + hash) + c) % MAX_TABLE;
	}
	return hash % MAX_TABLE;
}

User user[MAX_USER];

int userCount;

int currenttime;

void Init()
{
	userCount = 0;
	currenttime = 0;
	hrp = 0;

	for (int i = 0; i < MAX_TABLE; i++)
	{
		head[i] = 0;
	}

	for (int i = 0; i < MAX_TIME; i++)
	{
		logoutUser[i] = 0;
	}

	for (int i = 0; i < 20; i++) {
		user[i].isLogout = false;
	}
}

void NewAccount(char id[11], char password[11], int defaulttime)
{
	strcpy(user[userCount].id, id);
	strcpy(user[userCount].password, password);
	user[userCount].defaulttime = defaulttime;
	user[userCount].logouttime = currenttime + defaulttime;
	user[userCount].isLogout = false;

	int hashkey = hash(id);
	head[hashkey] = appendListNode(userCount, head[hashkey]);

	logoutUser[user[userCount].logouttime] = appendListNode(userCount,
		logoutUser[user[userCount].logouttime]);

	userCount++; // 사용자숫자 이기도 하고 새로운 사용자가 입력되어야할 Index
}

int getUserIdx(char id[11])
{
	int hashkey = hash(id);

	ListNode* temp = head[hashkey];
	while (temp != 0) {
		int i = temp->id;
		if (strcmp(user[i].id, id) == 0)
			return i;
		temp = temp->next;
	}

	/*for (int i = 0; i < userCount; i++)
	{
		if (strcmp(user[i].id, id) == 0)
			return i;
	}*/

	return -1;
}

void Logout(char id[11])
{
	/*id 에 해당 하는 계정의 서버 접속을 종료한다.
		서버에 login 되어 있지 않는 계정은 아무런 동작도 하지 않는다.*/
	int uIdx = getUserIdx(id);

	if (uIdx == -1)
		return;
	if (user[uIdx].isLogout)
		return;

	user[uIdx].isLogout = true;

}

void Connect(char id[11], char password[11])
{
	int uIdx = getUserIdx(id);
	if (uIdx == -1)
		return;
	if (user[uIdx].isLogout)
		return;
	if (strcmp(user[uIdx].password, password) != 0)
		return;
	user[uIdx].logouttime = currenttime + user[uIdx].defaulttime;
	//if(user[uIdx].logouttime <= 50000)
	logoutUser[user[uIdx].logouttime] = appendListNode(uIdx,
		logoutUser[user[uIdx].logouttime]);
}

int Tick()
{
	int logoutUserCount = 0;
	currenttime++;

	ListNode* temp = logoutUser[currenttime];

	while (temp != 0) {
		int i = temp->id;

		if (!user[i].isLogout && user[i].logouttime == currenttime)
		{
			user[i].isLogout = true;
			logoutUserCount++;
		}
		temp = temp->next;
	}

	/*for (int i = 0; i < userCount; i++)
	{
		if (user[i].isLogout)
			continue;

		if (user[i].logouttime == currenttime)
		{
			user[i].isLogout = true;
			logoutUserCount++;
		}
	}*/
	return logoutUserCount;
}

```
