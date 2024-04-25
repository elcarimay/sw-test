```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
# include <iostream>
# include <vector>
#include <stdio.h>
#include <iostream>
#include <queue>
using namespace std;

const int psize = 100;

struct Data
{
	int col, len;
	bool operator<(const Data&data)const {
		return col > data.col;
	}
};

int PN, N;
vector<Data> blank[20005];
int rowMax[20005], pageMax[205];

struct Word
{
	int r, c, len; // 단어 등록된 행,열,크기
	void erase() {
		auto&v = blank[r];
		int i = lower_bound(v.begin(), v.end(), Data{ c,0 }) - v.begin();

		// 오른쪽 노드 합치기
		if (i < v.size() && c + len == v[i].col)
			v[i] = { c, len + v[i].len };
		else
			v.insert(v.begin() + i, { c, len });

		// 왼쪽 노드 합치기
		if (i && v[i - 1].col + v[i - 1].len == c) {
			v[i - 1].len += v[i].len;
			v.erase(v.begin() + i);
			i--;
		}
		rowMax[r] = max(rowMax[r], v[i].len);
		pageMax[r / psize] = max(pageMax[r / psize], v[i].len);

		r = -1;
	}
}word[55005];

void init(int n, int m) {
	N = n;
	PN = (n - 1) / psize + 1;
	for (int i = 0; i < n; i++) {
		rowMax[i] = m;
		pageMax[i / psize] = m;
		blank[i] = { {0,m} };
	}
	fill(word, word + 55001, Word{ -1 });
}

int writeWord(int mId, int mLen) {
	for (int p = 0; p < PN; p++) {
		if (pageMax[p] < mLen)continue;
		int sr = p * psize;
		int er = min(sr + psize, N);
		for (int r = sr; r < er; r++) {
			if (rowMax[r] < mLen) continue;
			auto& v = blank[r];
			for (int i = 0; i < v.size(); i++) {
				if (v[i].len < mLen) continue;
				int c = v[i].col;
				word[mId] = { r,c,mLen };

				v[i] = { c + mLen, v[i].len - mLen };
				if (v[i].len == 0) v.erase(v.begin() + i);

				int maxLen = 0;
				for (auto p : v) maxLen = max(maxLen, p.col);
				rowMax[r] = maxLen;

				maxLen = 0;
				for (int j = sr; j < er; j++)
					maxLen = max(maxLen, rowMax[j]);
				pageMax[p] = maxLen;

				return r;
			}
		}
	}
	return -1;
}

int eraseWord(int mId) {
	int res = word[mId].r;
	if (res != -1) word[mId].erase();
	return res;
}
#endif
```
