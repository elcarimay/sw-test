```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <string.h>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

#define INF 987654321

vector<string> d;
int map[8][8];
int main() {
	// char c[] = "(((0101)(0110)(1100)1)((0101)(0110)(1100)1)((0101)(0110)(1100)1)((0101)(0110)(1100)1))";
	char c[] = "((1010)(1010)(1010)(1010))";
	char deli[] = "()";
	char* p = strtok(c, deli); // 0101
	d.push_back(p);
	int n = 8;
	while (1) {
		p = strtok(nullptr, deli); // 0110
		if (p == NULL) break;
		d.push_back(p);
	}
	
	int total_num = n * n; // 전체갯수
	int num = d.size(); // 분류갯수
	int lev = total_num / num / 4; // 단계

	int cnt = 0;
	while (lev != 4) {
		lev = total_num / num;
		cnt++;
	}

	vector<string> temp; 
	
	temp.resize(d.size());
	copy(d.begin(), d.end(), temp.begin());
	d.resize(d.size() * 4);
	cnt = 0; fill(d.begin(), d.end(), "\0");
	for (int i = 0; i < temp.size(); i++) {
		if (temp[i].size() == 4)
			for (int j = 0; j < 4; j++) {
				d[cnt++].push_back(temp[i][j]);
			}
		else
			for (int j = 0; j < 4; j++) {
				d[cnt++].push_back(temp[i][0]);
			}
	}

	temp.resize(d.size());
	copy(d.begin(), d.end(), temp.begin());
	d.resize(d.size() * 4);
	cnt = 0; fill(d.begin(), d.end(), "\0");
	for (int i = 0; i < temp.size(); i++) {
		if (temp[i].size() == 4)
			for (int j = 0; j < 4; j++) {
				d[cnt++].push_back(temp[i][j]);
			}
		else
			for (int j = 0; j < 4; j++) {
				d[cnt++].push_back(temp[i][0]);
			}
	}

	for (int i = 0; i < 8; i++)
		for (int j = 0; j < 8; j++)
			map[i][j] = stoi(d[i + j]);


	return 0;
}
#endif
```
