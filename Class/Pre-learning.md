# Problem_27160  
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
