```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <string.h>
#include <vector>
using namespace std;

int n;
struct Data
{
	char str[33];
	int cnt;
}D[20003];
vector<int> htab[33][26 * 26]; // htab[길이][str의 앞 두자리 26진법]

int gethash(char* s) { // s의 앞 두자리로 hash 값 생성(a = 0, z = 25)
	int hash = 0;
	for (int i = 0;i < 2;i++) hash = hash * 26 + s[i] - 'a';
	return hash;
}

void init() {
	n = 0;
	for (int i = 5;i <= 30;i++)
		for (auto& v : htab[i]) v.clear();
}

int add(char str[]) {
	int len = strlen(str);
	auto& v = htab[len][gethash(str)];
	for (int i : v)
		if (!strcmp(D[i].str + 2, str + 2))
			return ++D[i].cnt; // str일치하는게 있는 경우

	v.push_back(n); // str일치하는게 없는 경우
	strcpy(D[n].str, str);
	return D[n++].cnt = 1;
}

bool get(char* a, char* b) {
	for (int i = 0;a[i];i++)
		if (b[i] != '?' && a[i] != b[i]) return 0; // 같을 가능성이 없을때
	return 1; // 같을 가능성이 있을때
}

int cmd, ret, len;
void dfs(int c, int hash, char* s) {
	if (c >= 2) {
		auto& v = htab[len][hash];
		for (int i = 0;i < v.size();) {
			auto& d = D[v[i]];
			if (get(d.str + 2, s)) {
				ret += d.cnt;
				if (cmd) {
					v[i] = v.back();
					v.pop_back();
					continue;
				}
			}
			i++;
		}
		return;
	}
	if (*s == '?')
		for (int i = 0;i < 26;i++) dfs(c + 1, hash * 26 + i, s + 1);
	else
		dfs(c + 1, hash * 26 + *s - 'a', s + 1);
}

int remove(char str[]) {
	cmd = 1, ret = 0;
	len = strlen(str);
	dfs(0, 0, str);
	return ret;
}

int search(char str[]) {
	cmd = ret = 0;
	len = strlen(str);
	dfs(0, 0, str);
	return ret;
}
#endif // 1

```
