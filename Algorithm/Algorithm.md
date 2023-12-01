## 구조체만을 사용한 Stack
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;

const int MX = 1'0000;

struct Stack
{
	int dat[MX];
	int pos;
	void init() { pos = -1; }
	void push(int val) { dat[++pos] = val; }
	int pop() {
		if (empty()) return -1;
		return dat[pos--];
	}
	int top() { 
		if (empty()) return -1;
		return dat[pos];
	}
	bool empty() { return (pos < 0); }
	int size() { return pos + 1; }
};

int main(void) {
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0);
	int n;	scanf("%d", &n);
	Stack s; s.init();
	while (n--)
	{
		string c;
		cin >> c;
		if (c == "push") {
			int t; scanf("%d", &t);
			s.push(t);
		}
		else if (c == "pop") printf("%d\n", s.pop());
		else if (c == "size") printf("%d\n", s.size());
		else if (c == "empty") printf("%s\n", s.empty() ? "true" : "false");
		else if (c == "top") printf("%d\n", s.top());
		else { printf("You entered it incorrectly...\n"); n++; }
	}
}
```
## Template을 사용한 Stack
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;

const int MX = 1'0000;

template<typename Type>
struct Stack
{
	Type items[MX];
	int index;
	void init() { index = -1; }
	void push(int val) { items[++index] = val; }
	int pop() {
		if (empty()) return -1;
		return items[index--];
	}
	int top() {
		if (empty()) return -1;
		return items[index];
	}
	bool empty() { return (index < 0); }
	int size() { return index + 1; }
};

int main(void) {
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0);
	int n;	scanf("%d", &n);
	Stack<int> s; s.init();
	while (n--)
	{
		string c;
		cin >> c;
		if (c == "push") {
			int t; scanf("%d", &t);
			s.push(t);
		}
		else if (c == "pop") printf("%d\n", s.pop());
		else if (c == "size") printf("%d\n", s.size());
		else if (c == "empty") printf("%s\n", s.empty() ? "true" : "false");
		else if (c == "top") printf("%d\n", s.top());
		else { printf("You entered it incorrectly...\n"); n++; }
	}
}
```
## Template을 사용한 Queue
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;

const int MX = 1'0000;

template<class Type>
struct Queue
{
	Type items[MX];
	int head, tail;
	void init() { head = tail = 0; }
	void push(int val) { items[tail++] = val; }
	int pop() {
		if (empty()) return -1;
		return items[head++];
	}
	int front() {
		if (empty()) return -1;
		return items[head];
	}
	bool empty() { return (head == tail); }
	int size() { return tail - head; }
};

int main(void) {
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0);
	int n;	scanf("%d", &n);
	Queue<int> s; s.init();
	while (n--)
	{
		string c;
		cin >> c;
		if (c == "push") {
			int t; scanf("%d", &t);
			s.push(t);
		}
		else if (c == "pop") printf("%d\n", s.pop());
		else if (c == "size") printf("%d\n", s.size());
		else if (c == "empty") printf("%s\n", s.empty() ? "true" : "false");
		else if (c == "front") printf("%d\n", s.front());
		else { printf("You entered it incorrectly...\n"); n++; }
	}
}
```
## Template을 사용한 Deque
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;

const int MX = 10'000;

template<class Type>
struct Deque
{
	Type items[2*MX + 1];
	int head, tail;
	void init() { head = tail = MX; }
	void push_front(int val) { items[--head] = val; }
	void push_back(int val) { items[tail++] = val; }
	int pop_front() {
		if (empty()) return -1;
		return items[head++]; 
	}
	int pop_back() {
		if (empty()) return -1;
		return items[tail--]; 
	}
	// void clear() { fill(items[head], items[tail], {}); }
	bool empty() { return (head == tail); }
	int size() { return tail - head; }
	int front() { 
		if (empty()) printf("-1\n");
		else return items[head]; 
	}
	int back() {
		if (empty()) printf("-1\n");
		else return items[tail - 1];
	}
};

int main(void) {
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0);
	int n;	scanf("%d", &n);
	Deque<int> s; s.init();
	while (n--)
	{
		string c;
		cin >> c;
		if (c == "push_front") {
			int t; scanf("%d", &t);
			s.push_front(t);
		}
		else if (c == "push_back") {
			int t; scanf("%d", &t);
			s.push_back(t);
		}
		else if (c == "pop_front") { printf("%d\n", s.pop_front()); }
		else if (c == "pop_back") { printf("%d\n", s.pop_back()); }
		else if (c == "empty") printf("%s\n", s.empty() ? "true" : "false");
		else if (c == "size") printf("%d\n", s.size());
		else if (c == "front") printf("%d\n", s.front());
		else if (c == "back") printf("%d\n", s.back());
		else { printf("You entered it incorrectly...\n"); n++; }
	}
}
```
