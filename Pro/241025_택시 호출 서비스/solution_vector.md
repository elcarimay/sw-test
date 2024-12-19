```cpp
#if 1
#include <algorithm>
#include <vector>
using namespace std;

struct Data {
	int RideDist, taxi_number; // 총 이동거리, 택시번호
	bool operator<(const Data& r)const {
		if (RideDist != r.RideDist) return RideDist > r.RideDist;
		return taxi_number < r.taxi_number;
	}
};

struct CloseData {
	int dist, taxi_number; // 출발지로부터의 거리, 택시번호
	bool operator<(const CloseData& r)const {
		if (dist != r.dist) return dist < r.dist;
		return taxi_number < r.taxi_number;
	}
};

struct Result {
	int mX, mY;
	int mMoveDistance; // 총 이동거리
	int mRideDistance; // 운행거리
};

Result db[2003];
vector<int> taxiList[10][10]; // 택시번호
vector<Data> tot;
int N, L, M;
void init(int N, int M, int L, int mXs[], int mYs[]) {
	::N = N, ::L = L, ::M = M, tot.clear();
	for (int i = 0; i < 10; i++) for (int j = 0; j < 10; j++) taxiList[i][j].clear();
	for (int i = 1; i <= M; i++) {
		db[i] = { mXs[i - 1], mYs[i - 1] };
		taxiList[mYs[i - 1] / L][mXs[i - 1] / L].push_back(i);
		tot.push_back({ 0, i });
	}
}

int dist(int cx, int cy, int nx, int ny) {
	return abs(cx - nx) + abs(cy - ny);
}

int pickup(int mSX, int mSY, int mEX, int mEY) {
	vector<CloseData> tmp;
	int sx = max((mSX - L) / L, 0), sy = max((mSY - L) / L, 0);
	int ex = min((mSX + L) / L , (N - L) / L), ey = min((mSY + L) / L, (N - L) / L);
	int dist_ts = 0, dist_se = 0; // taxi to start, start to end
	for (int i = sx; i <= ex; i++) for (int j = sy; j <= ey; j++)
		for (int k = 0; k < taxiList[j][i].size(); k++) {
			auto& d = db[taxiList[j][i][k]];
			dist_ts = dist(mSX, mSY, d.mX, d.mY);
			if (dist_ts <= L) tmp.push_back({ dist_ts, taxiList[j][i][k] });
		}
	if (!tmp.empty()) {
		partial_sort(tmp.begin(), tmp.begin() + 1, tmp.end());
		int taxi_number = tmp[0].taxi_number;
		auto& d = db[taxi_number];
		// 택시 이동 처리
		auto& t = taxiList[d.mY / L][d.mX / L];
		for (int k = 0; k < t.size(); k++) {
			if (t[k] != taxi_number) continue;
			auto& d1 = db[t[k]];
			t.erase(t.begin() + k);
			dist_ts = dist(mSX, mSY, d.mX, d.mY);
			dist_se = dist(mSX, mSY, mEX, mEY);
			d1.mMoveDistance += dist_ts + dist_se;
			d1.mRideDistance += dist_se;
			d1.mX = mEX, d1.mY = mEY;
			taxiList[d1.mY / L][d1.mX / L].push_back(taxi_number);
			for (int i = 0; i < tot.size(); i++)
				if (tot[i].taxi_number == taxi_number) {
					tot[i].RideDist = d1.mRideDistance; return taxi_number;
				}
		}
	}
	return -1;
}

Result reset(int mNo) {
	auto& d = db[mNo];
	Result res = { d.mX, d.mY, d.mMoveDistance, d.mRideDistance };
	d.mMoveDistance = d.mRideDistance = 0;
	for (int i = 0; i < tot.size(); i++)
		if (tot[i].taxi_number == mNo) {
			tot[i].RideDist = d.mRideDistance; return res;
		}
}

void getBest(int mNos[]) {
	partial_sort(tot.begin(), tot.begin() + 5, tot.end());
	for (int i = 0; i < 5; i++) mNos[i] = tot[i].taxi_number;
}
#endif // 0

```
