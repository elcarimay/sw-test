```cpp
#if 1 // 강사코드에서 참조(&)를 없애고 수정함.
#include<unordered_map>
#include<string>
using namespace std;

const int MOD = 10000;
const int BASE = 1001;

int LUT[10][1003];
unordered_map<string, unordered_map<int, int>> poly;
// { name, { X지수*1001+Y지수, 계수 } }

void init()
{
	poly.clear();
	if (LUT[0][0] == 0) {
		for (int i = 0; i <= 8; i++) {
			LUT[i][0] = 1;
			for (int j = 1; j <= 1000; j++)
				LUT[i][j] = (LUT[i][j - 1] * (i - 4) % MOD + MOD) % MOD;
		}
	}
}

void assign(char mName[], char mPoly[])
{
	int c = 1, x = 0, y = 0;	// c=계수, x=X지수, y=Y지수
	int type = 0;				// 계수:0, X지수:1, Y지수:2
	for (int i = 0; mPoly[i]; i++) {
		if (mPoly[i] == '+' || mPoly[i] == '-') {
			poly[mName][x * BASE + y] = (poly[mName][x * BASE + y] + c + MOD) % MOD;
			type = x = y = 0;
			c = (mPoly[i] == '+' ? 1 : -1);
		}
		else if (mPoly[i] == 'X') type = 1, x = 1;
		else if (mPoly[i] == 'Y') type = 2, y = 1;
		else if (mPoly[i] == '^') continue;
		else {
			int num = mPoly[i] - '0';
			if (type == 0) c *= num;
			else if (type == 1) x *= num;
			else y *= num;
		}
	}
	poly[mName][x * BASE + y] = (poly[mName][x * BASE + y] + c + MOD) % MOD;
}

void compute(char mNameR[], char mNameA[], char mNameB[], int mOp)
{
	if (mOp == 0) {
		for (auto p: poly[mNameA])
			poly[mNameR][p.first] = (poly[mNameR][p.first] + p.second + MOD) % MOD;
		for (auto p : poly[mNameB])
			poly[mNameR][p.first] = (poly[mNameR][p.first] + p.second + MOD) % MOD;
	}
	else if (mOp == 1) {
		for (auto p : poly[mNameA])
			poly[mNameR][p.first] = (poly[mNameR][p.first] + p.second + MOD) % MOD;
		for (auto p : poly[mNameB])
			poly[mNameR][p.first] = (poly[mNameR][p.first] - p.second + MOD) % MOD;
	}
	else {
		for (auto p : poly[mNameA]) for (auto q : poly[mNameB])
			poly[mNameR][p.first + q.first] = (poly[mNameR][p.first + q.first] + p.second * q.second + MOD) % MOD;
	}
}

int getCoefficient(char mName[], int mDegreeX, int mDegreeY)
{
	return poly[mName][mDegreeX * BASE + mDegreeY];
}

int calcPolynomial(char mName[], int mX, int mY)
{
	int ret = 0;
	for (auto p : poly[mName]) {
		int x = p.first / BASE, y = p.first % BASE, c = p.second;
		ret = (ret + (long long)c * LUT[mX + 4][x] * LUT[mY + 4][y]) % MOD;
	}
	return ret;
}
#endif
```
