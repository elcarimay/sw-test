## 백준 1654번 랜선자르기
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <vector>
using namespace std;

inline int min(int a, int b) { return (a < b) ? a : b; }
inline int max(int a, int b) { return (a < b) ? b : a; }

long long NUMS;
vector<long long> arr;
long long target;

bool condition(long x, long long target) {
	long long cnt = 0;
	for (int i = 0; i < NUMS; i++)
	{
		cnt += arr[i] / x;
	}
	return cnt >= target;
}

// [최적화 문제] target 조건을 만족하는 최대값 구하기
int search(long long low, long long high, long long target) {
	long long sol = low;
	while (low <= high) {
		long long mid = (low + high) / 2;

		// [결정문제] 자른 랜선의 길이가 x 일때, 전체 랜선의 개수가 target개 이상인가? -> 자르는 랜선 길이 증가
		if (condition(mid, target)) {
			sol = mid;
			low = mid + 1;
		}
		else high = mid - 1;
	}
	return sol;
}

int main() {
	long long low = 1;
	long long high = 0;
	scanf("%d %d", &NUMS, &target);
	int num;
	for (int i = 0; i < NUMS; i++)
	{
		scanf("%d", &num);
		arr.push_back(num);
		high = max(high, num);
	}
	printf("%d\n", search(low, high, target));
	return 0;
}
```

## 백준 1939번 중량(STL)
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <queue>
#include <iostream>
#include <cstdio>
using namespace std;

const int N = 100'010; // N 개의 섬

struct Node
{
	int to, weight;
};
vector<Node> nodes[N];

// [결정문제] 선택한 중량값으로 모든 다리를 건널수 있는가?(bfs)
bool condition(int start, int end, int weight) {
	queue<Node> Q;
	Q.push({ start, 0 });
	vector<bool> visited(N, false);
	visited[start] = true;
	while (Q.size())
	{
		auto cur = Q.front(); Q.pop();
		for (auto next:nodes[cur.to])
		{
			if (!visited[next.to] && next.weight >= weight) {
				visited[next.to] = true;
				Q.push(next);
			}
		}
	}
	return visited[end] ? true : false;
}

// [최적화문제] 모든 다리를 건널수 있는 최대 중량값은?
int search(int start, int end, int low, int high) {
	int sol = low;
	while (low <= high)
	{
		int mid = (low + high) / 2;
		if (condition(start, end, mid)) {
			sol = mid;
			low = mid + 1;
		}
		else high = mid - 1;
	}
	return sol;
}

int main() {
	int n, m;
	scanf("%d %d", &n, &m);
	for (int i = 0; i < m; i++)
	{
		int a, b, w;
		scanf("%d %d %d", &a, &b, &w);
		nodes[a].push_back({ b,w });
		nodes[b].push_back({ a,w });
	}
	int a, b;
	scanf("%d %d", &a, &b);
	printf("%d\n", search(a, b, 1, 1'000'000'000));
	return 0;
}
```

## 백준 1939번 중량(Manual)
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <queue>
#include <iostream>
#include <cstdio>
using namespace std;

template<typename Type>
struct LinkedList {
	struct ListNode {
		Type data;
		ListNode* next;
	};
	ListNode* head;
	ListNode* tail;

	void clear() { head = nullptr; tail = nullptr; }
	void push_back(const Type& data) {
		ListNode* node = new ListNode({ data, nullptr });
		if (head == nullptr) { head = node; tail = node; }
		else { tail->next = node; tail = node; }
	}
};

template<typename Type>
struct Queue {
	LinkedList<Type> Q;
	int queueSize;

	void clear() { Q.clear(); queueSize = 0; }
	void push(const Type& data) {
		Q.push_back(data);
		queueSize += 1;
	}
	void pop() {
		auto* temp = Q.head;
		Q.head = temp->next;
		delete temp;
		queueSize -= 1;
	}
	bool empty() { return queueSize == 0; }
	Type front() { return Q.head->data; }
	int size() { return queueSize; }
};

template<typename Type>
struct Stack {
	LinkedList<Type> S;
	int stackSize;
	struct ListNode {
		Type data;
		ListNode* next;
	};
	ListNode* head;
	ListNode* tail;
	void clear() { S.clear(); stackSize = 0; }
	void push(const Type& data) {
		ListNode* node = new ListNode({ data, nullptr });
		if (head == nullptr) { head = node; tail = node; }
		else { node->next = head; head = node; }
		stackSize += 1;
	}
	void pop() {
		auto* temp = S.head;
		S.head = temp->next;
		delete temp;
		stackSize -= 1;
	}
	bool empty() { return stackSize == 0; }
	Type top() { return S.head->data; }
};

const int N = 100'010; // N 개의 섬

struct Node
{
	int to, weight;
};
vector<Node> nodes[N];

// [결정문제] 선택한 중량값으로 모든 다리를 건널수 있는가?(bfs)
bool condition(int start, int end, int weight) {
	//queue<Node> Q;
	Queue<Node> Q;
	Q.push({ start, 0 });
	vector<bool> visited(N, false);
	visited[start] = true;
	while (Q.size())
	{
		auto cur = Q.front(); Q.pop();
		for (auto next : nodes[cur.to])
		{
			if (!visited[next.to] && next.weight >= weight) {
				visited[next.to] = true;
				Q.push(next);
			}
		}
	}
	return visited[end] ? true : false;
}

// [최적화문제] 모든 다리를 건널수 있는 최대 중량값은?
int search(int start, int end, int low, int high) {
	int sol = low;
	while (low <= high)
	{
		int mid = (low + high) / 2;
		if (condition(start, end, mid)) {
			sol = mid;
			low = mid + 1;
		}
		else high = mid - 1;
	}
	return sol;
}

int main() {
	int n, m;
	scanf("%d %d", &n, &m);
	for (int i = 0; i < m; i++)
	{
		int a, b, w;
		scanf("%d %d %d", &a, &b, &w);
		nodes[a].push_back({ b,w });
		nodes[b].push_back({ a,w });
	}
	int a, b;
	scanf("%d %d", &a, &b);
	printf("%d\n", search(a, b, 1, 1'000'000'000));
	return 0;
}
```

## [BOJ] 2110번 공유기설치
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <vector>
#include <cstdio>
#include <algorithm>
using namespace std;

vector<int> house;

bool condition(int x, int target) {
	int cnt = 1; // 공유기 설치갯수
	int cur = house[0]; // 공유기 설치 위치
	for (int i = 1; i < house.size(); i++)
	{
		if (house[i] - cur >= x) {
			cnt++;
			cur = house[i];
		}
	}
	return cnt >= target;
}

// [최적화 문제] 공유기 사이 거리 target 조건을 만족하는 최대값 구하기
int search(int low, int high, int target) {
	int sol = low;
	while (low <= high)
	{
		int mid = (high + low) / 2;
		if (condition(mid, target)) {
			sol = mid;
			low = mid + 1;
		}
		else
		{
			high = mid - 1;
		}
	}
	return sol;
}

int main() {
	int n, c, num; scanf("%d %d", &n, &c);
	for (int i = 0; i < n; i++) {
		scanf("%d", &num);
		house.push_back({ num });
	}
	sort(house.begin(), house.end());
	int low = 1;
	int high = house[n - 1] - house[0];
	printf("%d", search(low, high, c));
	return 0;
}
```

## [BOJ] 2470 두 용액(파라메트릭 서치)
```cpp
// https://loosie.tistory.com/322
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <algorithm>
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;

const int N = 100'000;
long long MIN = 2'000'000'001;

int arr[N + 10];

struct Result
{
	int val1, val2;
};

Result ans;

// [결정문제] choice 값과 x에서의 합의 절대값이 Min값보다 작은가?
bool condition(int sum_value) {
	return abs(MIN) > abs(sum_value);
}

//[최적화 문제] choice 값과의 합의 절대값이 최소가 되는 값의 인덱스는?
void search(int low, int high) {
	while (low < high) {
		long long sum = arr[low] + arr[high];
		if (condition(sum)) {
			MIN = sum;
			ans.val1 = arr[low];
			ans.val2 = arr[high];
		}
		if (sum == 0) break;
		else if (sum > 0) --high;
		else ++low;
	}
	return;
}

int main() {
	int n; scanf("%d", &n);
	for (int i = 0; i < n; i++) scanf("%d", &arr[i]);
	sort(arr, arr + n); // 배열인 경우 → sort(arr.begin(), arr.end());
	int low = 0, high = n - 1;
	search(low, high);
	printf("%d %d\n", ans.val1, ans.val2);
	return 0;
}
```

## [BOJ] 2512 예산
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

inline int min(int a, int b) { return (a < b) ? a : b; }
inline int max(int a, int b) { return (a < b) ? b : a; }

vector<int> arr;
int target;

// [결정문제] 상한액 금액이 정해졌을때 총 예산금액을 넘지 않는가?
bool condition(int x) {
	int sum = 0;
	for (auto m : arr) { sum += min(m, x); }
	return sum <= target;
}

// [최적화 문제] 모든 지방 예산의 합이 총 예산금액을 넘지않는 최대 상한액?
int search(int low, int high, int target) {
	int sol = low;
	while (low <= high)
	{
		int mid = (low + high) / 2;
		if (condition(mid)) {
			sol = mid;
			low = mid + 1;
		}
		else high = mid - 1;
	}
	return sol;
}


int main() {
	int n, num; scanf("%d", &n);
	for (int i = 0; i < n; i++)
	{
		scanf("%d", &num);
		arr.push_back(num);
	}
	scanf("%d", &target);
	sort(arr.begin(), arr.end());
	int low = arr[0]; int high = arr[n - 1];
	printf("%d\n", search(low, high, target));
	return 0;
}
```

## [BOJ] 2805번 나무 자르기
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

vector<int> tree;
int height;

// [결정문제] 절단 높이가 주어졌을때, 조건을 만족하는가?
bool condition(int x, int target) {
	long long sum = 0; // long long 으로 해야 통과됨
	for (auto m : tree) 
		if (x <= m) sum += m - x;
	return sum >= target;
}

// [최적화 문제] 최대 절단 높이는?
int search(int low, int high, int target) {
	int sol = low;
	while (low <= high)
	{
		int mid = (low + high) / 2;
		if (condition(mid, target)) {
			sol = mid;
			low = mid + 1;
		}
		else
		{
			high = mid - 1;
		}
	}
	return sol;
}

int main() {
	int n, height, num; scanf("%d %d", &n, &height);
	for (int i = 0; i < n; i++)
	{
		scanf("%d", &num);
		tree.push_back(num);
	}
	sort(tree.begin(), tree.end());
	int low = 0; int high = tree[n - 1];
	printf("%d\n", search(low, high, height));
	return 0;
}
```
