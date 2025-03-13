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
```
