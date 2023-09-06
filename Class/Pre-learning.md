# 우선순위큐
## Problem_27160  
```cpp
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main() {
	ios_base::sync_with_stdio(0);
	cin.tie(0); cout.tie(0);
	int n;
	cin >> n;
	map<string, int> map;
	while (n--) {
		string fruit;
		int num;
		cin >> fruit >> num;
		map[fruit] += num;
	}
	for (auto& m:map)
	{
		if (m.second == 5) {
			cout << "YES";
			return 0;
		}
	}
	cout << "NO";
}
```

## Problem_11286  
```cpp
#include <iostream>
#include <queue>
#include <time.h>
#include <vector>
using namespace std;

struct cmp
{
	bool operator()(int a, int b) {
		if (abs(a) == abs(b))
			return a > b;
		else
			return abs(a) > abs(b);
	}
};

int main()
{
	// ios::sync_with_stdio(false);
	clock_t start = clock();
	vector<int> result;
	int n, x;
	cin >> n;

	priority_queue<int, vector<int>, cmp> q;
	for (int i = 0; i < n; i++)
	{
		cin >> x;
		if (x != 0) {
			q.push(x);
		}
		else {
			if (q.empty()) {
				result.push_back(0);
			}
			else {
				result.push_back(q.top());
				q.pop();
			}
		}
	}
	for (int i = 0; i < result.size(); i++)
	{
		cout << result[i] << '\n';
	}
	int time_result = (clock() - start) / (CLOCKS_PER_SEC / 1000);
	printf("\n>> Result: %d ms\n", time_result);
	return 0;
}
```
