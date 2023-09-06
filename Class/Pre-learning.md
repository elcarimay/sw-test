# 우선순위큐
## Problem_11279  
```cpp
#include <iostream>
#include <queue>
#include <time.h>
#include <vector>
using namespace std;


int main()
{
	// ios::sync_with_stdio(false);
	clock_t start = clock();
	vector<int> result;
	int n, x;
	cin >> n;

	priority_queue<int, vector<int>> q;
	for (int i = 0; i < n; i++)
	{
		cin >> x;
		if (x != 0) {
			q.push(x);
		}
		else {
			if (q.empty()) {
				result.push_back(0);
			}else{
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
## Problem_1927
```cp
#include <iostream>
#include <queue>
#include <time.h>
#include <vector>
using namespace std;

struct cmp
{
	bool operator()(int a, int b){
		return a > b;
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
	//priority_queue<int, vector<int>, greater<int>> q;
	for (int i = 0; i < n; i++)
	{
		cin >> x;
		if (x != 0) {
			q.push(x);
		}
		else {
			if (q.empty()) {
				result.push_back(0);
			}else{
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
# 해쉬  
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
## Problem_4158  
```cpp
#include <iostream>
#include <string>
#include <unordered_map>
#define fastio ios_base::sync_with_stdio(0); cin.tie(0); cout.tie(0);

using namespace std;

int main() {
	int n, m, ans;
	unordered_map<int, int> sangeunCD;

	fastio;

	while (1) {
		cin >> n >> m;
		if (n == 0 && m == 0) break;
		ans = 0;
		sangeunCD.clear();
		for (int i = 0, cd; i < n; i++)
		{
			cin >> cd;
			sangeunCD[cd] = 1;
		}

		for (int i = 0, sunyoungCD; i < m; i++) {
			cin >> sunyoungCD;
			if (sangeunCD[sunyoungCD]) ans++;
		}
		cout << ans << '\n';
	}
}
```  
## Problem_1620  
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <map>

using namespace std;

int main() {
	map<string, int> pokemon;
	vector<string> name;
	int n, m;
	string temp;
	cin >> n >> m;
	vector<string> result;
	for (int i = 1; i <= n; i++)
	{
		cin >> temp;
		name.push_back(temp);
		pokemon.insert(make_pair(temp, i));
	}
	for (int i = 0; i < m; i++)
	{
		cin >> temp;
		if (temp[0] >= 65 && temp[0] <= 90) {
			// 포켓몬 이름이 주어진 경우(맨 앞 문자가 영어 대문자인 것을 이용)
			result.push_back(to_string(pokemon[temp]));
		}
		else {
			result.push_back(name[stoi(temp) - 1]);
			// 입력값이 string이므로 정수형으로 변환후 넣어줌
			// -1을 해준이유는 name배열이 0부터 시작했기 때문
		}
	}
	for (int i = 0; i < result.size(); i++)
	{
		cout << result[i] << '\n';
	}
	return 0;
}
```  
## Problem_1764  
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <map>

using namespace std;

int main() {
	int n, m, cnt = 0;
	string s;
	vector<string> result;
	map<string, bool> list;
	cin >> n >> m;
	for (int i = 0; i < n; i++)
	{
		cin >> s;
		list.insert(make_pair(s, true));
		// 해시 맵에 듣도 못한 사람 넣어줌
	}
	for (int i = 0; i < m; i++)
	{
		cin >> s;
		if (list[s]) {
			//해시맵에 보도못한 사람이 있으면
			result.push_back(s);
			cnt++;
		}
	}
	cout << cnt << '\n';
	sort(result.begin(), result.end());
	for (int i = 0; i < result.size(); i++)
	{
		cout << result[i] << '\n';
	}
	return 0;
}
```  
