```cpp
// Unordered_map도 직접 구현
//Overview
//1. 자료형 - Kitchen, Order
//2. Kitchen에 Queue가 필요하다(que에는 order의 Index값을 저장), que는 단순 array
//3. Order에 완성된 음식 List를 저장할 array가 필요하다.
//4. 구현할 함수2개 cancel과 getstatus에서 order의 mid로 order정보를 얻어야 한다.(1 <= mID <= 1, 000, 000, 000)
//->Hash 자료구조 필요
//5. Kitchen은 ID없고 Index가 곧 음식의 종류이기 때문에 hash가 불필요
//6. 주문이 add, canel될때 경계 처리 주의 필요
//7. 전체 남아있는 주문의 개수 변수 설정해서 관리 필요
//
//Algorithm
//자료구조 최적화 문제풀이 주의사항
//- STL 사용 최소화(STL은 동적할당을 하기때문에 느려지기 때문)
//- 정적 할당 array 사용
//- C 스타일 코딩 사용, class 자료형 사용 최소화
//
//Timestamp문제풀이 주의사항
//- Ticking순이 아닌 Event형식으로 문제풀이
//	: 주문을 한 시점 order.t = mTime
//	: 요리가 끝나는 시점 Kitchen.t = mTime + kitchen, cooktime
//
//	kitchen.t 바로 다음 음식이 완성되는 시점
//	order.t 주문이 들어온 시점
//
//	mTime 이벤트 < t : 아직 음식이 완성되지 않았으므로 queue 처리 불필요
//	t < mTime : 음식이 완성되었으므로 queue 처리필요

#define MAXO 15001
#define MAXN 11
#define SIZEH 1<<15

struct Kitchen {
	int t;			// 바로 다음 음식이 완성되는 시점 next time of food complete
	int cooktime;	// 음식이 걸리는 시간(amount) cooking time
	int queue[MAXO]; // queue for order
	int qs, qe;		 // start/end index of queue
};
int N;				// kitchen 개수
Kitchen kits[MAXN];	// kitchen array

struct Order {
	int inext;		// hash
	int mID;		// 주문 id 번호
	int t;			// 주문이 들어온 시점
	int ct;			// 주문이 취소된 시점 canceled time
	int m;			// 남아있는 주문의 개수 # of food remained
	int lcnt;		// 완성된 음식개수 # of food done
	int list[MAXN - 1]; // 완성된 음식 저장 배열  Order.list[order.lcnt++] = k;
};

int ocnt;			// 주문의 개수
Order orders[MAXO];	// order array
int hash[SIZEH];

int numorder;

Order& getOrder(int mID) {
	int hidx = mID & (SIZEH - 1);
	// int hidx = mID % SIZEH; // 나머지 연산을 하는건데 위에 꺼가 더 빠름
	int oidx = hash[hidx];
	for (; mID != orders[oidx].mID; oidx = orders[oidx].inext); // 덮어쒸어졌으면 이전값으로 리턴

	return orders[oidx];
}

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
				if(t < order.ct) { // 취소되기전 음식이 만들어진 경우
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
		kit.t = 0;
		kit.cooktime = mCookingTimeList[i - 1];
		kit.qs = kit.qe = 0;
	}

	// init hash
	for (int h = 0; h < SIZEH; ++h) hash[h] = 0;
}

int order(int mTime, int mID, int M, int mDishes[]) {
	run(mTime);

	// add order
	Order& order = orders[++ocnt];
	order.t = mTime;
	order.mID = mID;
	order.m = M;
	order.ct = 0;
	order.lcnt = 0;

	// hash table
	int hidx = mID & (SIZEH - 1);
	order.inext = hash[hidx];
	hash[hidx] = ocnt;

	// add order to kitchen queue
	for (int i = 0; i < M; ++i) {
		Kitchen& kit = kits[mDishes[i]];
		kit.queue[kit.qe++] = ocnt;
		if (kit.t == 0) kit.t = mTime + kit.cooktime;
	}
	return ++numorder;
}

int cancel(int mTime, int mID) {
	Order& order = getOrder(mID);
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
	Order& order = getOrder(mID);
	return order.ct? -1:order.m;
}
```
