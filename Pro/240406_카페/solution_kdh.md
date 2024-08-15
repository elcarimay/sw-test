```cpp
#if 1
#include <unordered_map>
using namespace std;

#define MAXO 15001
#define MAXN 11

unordered_map<int, int> m;

struct Kitchen {
	int t;			// 바로 다음 음식이 완성되는 시점 next time of food complete
	int cooktime;	// 음식이 걸리는 시간(amount) cooking time
	int queue[MAXO]; // queue for order
	int qs, qe;		 // start/end index of queue
};
int N;				// kitchen 개수
Kitchen kits[MAXN];	// kitchen array

struct Order {
	int mID;		// 주문 id 번호
	int t;			// 주문이 들어온 시점
	int ct;			// 주문이 취소된 시점 canceled time
	int m;			// 남아있는 주문의 개수 # of food remained
	int lcnt;		// 완성된 음식개수 # of food done
	int list[MAXN - 1]; // 완성된 음식 저장 배열  Order.list[order.lcnt++] = k;
};

int ocnt;			// 주문의 개수
Order orders[MAXO];	// order array
int numorder;

void run(int mTime) {
	for (int k = 1; k <= N; ++k) {
		Kitchen& kit = kits[k];
		int& t = kit.t;
		int& cooktime = kit.cooktime;
		int& qs = kit.qs;
		int& qe = kit.qe;
		int* que = kit.queue;
		while (qs != qe) {
			Order& order = orders[que[qs]];
			if (order.ct) { // cancel 되었을때
				if (t < order.ct) { // 취소되기전 음식이 만들어진 경우
					order.list[order.lcnt++] = k;
					t += cooktime;
				}
			}
			else { // cancel이 안되었을때
				if (mTime < t) break; // t: 음식이 완성된 시간, mTime: 이벤트 시간

				// 음식이 완성되었을때 t <= mTime
				order.list[order.lcnt++] = k;
				if (--order.m == 0) --numorder;
				t += cooktime;
			}
			if (++qs == qe) t = 0;
		}
	}
}

void init(int N, int mCookingTimeList[]) {
	::N = N, ocnt = numorder = 0;

	// kitchen setting
	for (int i = 1; i <= N; ++i) {
		Kitchen& kit = kits[i];
		kit.cooktime = mCookingTimeList[i - 1];
		kit.t = kit.qs = kit.qe = 0;
	}
	m.clear();
}

int order(int mTime, int mID, int M, int mDishes[]) {
	run(mTime);

	// add new order
	Order& order = orders[++ocnt];
	m[mID] = ocnt;
	order.t = mTime;
	order.mID = mID;
	order.m = M;
	order.ct = order.lcnt = 0;

	// add order to kitchen queue
	for (int i = 0; i < M; ++i) {
		Kitchen& kit = kits[mDishes[i]];
		kit.queue[kit.qe++] = ocnt;
		if (kit.t == 0) kit.t = mTime + kit.cooktime;
	}
	return ++numorder;
}

int cancel(int mTime, int mID) {
	Order& order = orders[m[mID]];
	order.ct = mTime; // 음식 cancel
	run(mTime);

	// 취소되었는데 만들어진 음식을 다음 큐에 재배정 rearrange next que
	for (int i = 0; i < order.lcnt; ++i) {
		int k = order.list[i];
		Kitchen& kit = kits[k];
		int& qs = kit.qs;
		int& qe = kit.qe;
		int* que = kit.queue;
		bool isalloc = false;
		while (qs != qe && !isalloc) {
			Order& norder = orders[que[qs]];
			if (!norder.ct) {
				norder.list[norder.lcnt++] = k;
				if (--norder.m == 0) --numorder;
				isalloc = true;
			}
			if (++qs == qe) { // que가 비었을때
				kit.t = 0;
			}
		}
	}
	return --numorder;
}

int getStatus(int mTime, int mID) {
	run(mTime);
	Order& order = orders[m[mID]];
	return order.ct ? -1 : order.m;
}
#endif // 1

```
