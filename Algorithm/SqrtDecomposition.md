```cpp
void build (){
	for (sq = 1; sq * sq < N; sq++); // sq = 블록크기 , sqrt N ) 올림한 값
	fill(maxA, maxA + N / sq, INF); // N sq = 블록개수
	for(int i = 0 ; i < N ; i++)
		maxA[i / sq ] = max(maxA[i / sq ], A[i]);
}

void update(int x , int val ){
	A[x] = val;
	int idx = x / sq ; // idx 블록 번호
	int l = idx * sq; // 블록의 범위 [l, r) : l<=i<r
	int r = min(l + sq , N);
	maxA[idx] = *max_element(A + l, A + r);
}

int query(int l , int r ) { // [l , r]
	int maxv = 0;
	/* 1) 왼쪽 자투리 */
	while(l <= r && l % sq)
		maxv = max(maxv, A[l++]);
	/* 2) 오른쪽 자투리 */
	while(l <= r &&(r + 1) % sq)
		maxv = max(maxv, A[r--]);
	/* 3) 모든 원소 포함되는 블록 */
	while(l <= r ){
		maxv = max(maxv, maxA[l / sq]);
		l += sq;
	}
	return maxv;
}

== Sample Code ==
int maxA[MAXN], minA[MAXN], sumTree[MAXN];
int N, sq;
int* A;
void build(int N, int mA[]) {
	::N = N, A = mA;
	for (sq = 1; sq * sq < N; sq++);
	for (int i = 0; i < N; i++) {
		int bid = i / sq;
		if (bid * sq == i) sumTree[bid] = 0, maxA[bid] = -INF, minA[bid] = INF;
		maxA[bid] = max(maxA[bid], A[i]);
		minA[bid] = min(minA[bid], A[i]);
		sumTree[bid] += A[i];
	}
}

void update(int x, int val) {
	int orgVal = A[x];
	A[x] += val;
	int bid = x / sq;
	int l = bid * sq;
	int r = min(l + sq, N);

	if (val < 0) {
		if (maxA[bid] == orgVal) maxA[bid] = *max_element(A + l, A + r);
		minA[bid] = min(minA[bid], A[x]);
	}
	else {
		maxA[bid] = max(maxA[bid], A[x]);
		if (minA[bid] == orgVal) minA[bid] = *min_element(A + l, A + r);
	}
	sumTree[bid] += val;
}

int sum_query(int l, int r) {
	int sumv = 0;
	while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumTree[l / sq], l += sq;
	return sumv;
}

int maxmin_query(int l, int r) {
	int maxv = -INF, minv = INF;
	while (l <= r && l % sq) maxv = max(maxv, A[l]), minv = min(minv, A[l++]);
	while (l <= r && (r + 1) % sq) maxv = max(maxv, A[r]), minv = min(minv, A[r--]);
	while (l <= r) maxv = max(maxv, maxA[l / sq]), minv = min(minv, minA[l / sq]), l += sq;
	return maxv - minv;
}
```
