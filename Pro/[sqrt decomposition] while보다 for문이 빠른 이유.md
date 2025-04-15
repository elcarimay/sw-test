```cpp
int sum_query1(int l, int r) {
	int sumv = 0;
	while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumA[l / sq], l += sq;
	return sumv;
}

int sum_query2(int l, int r) {
	int sumv = 0;
	int sbid = l / sq, ebid = r / sq;
	if (sbid == ebid){
		for (int i = l; i <= r; i++) sumv += A[i];
		return sumv;
	}
	int start_block_end = (sbid + 1) * sq - 1;
	for (int i = l; i <= start_block_end; i++) sumv += A[i];
	int end_block_start = ebid * sq;
	for (int i = end_block_start; i <= r; i++) sumv += A[i];
	for (int i = sbid + 1; i <= ebid - 1; i++) sumv += sumA[i];
	return sumv;
}

// sum_query2가 더 빠른 이유  
// 분기 수가 적고 단순 루프 구조 -> CPU 예측 + 캐시 활용 좋음
// 메모리 접근이 순차적
// 컴파일러 최적화에 유리함
✅ 왜 sum_query2가 더 빠를 수 있을까?

1. 분기(branch) 수가 적다

sum_query1은 while 문이 3개 있고, 각 while에서 매번 조건 검사 (l <= r, l % sq, r % sq)가 반복됩니다.

이건 CPU 입장에서 분기(branch) 예측 실패를 일으킬 가능성이 더 큽니다.


> if/while 문이 많을수록 CPU가 다음 실행 경로를 예측하기 어려워져서 속도 저하 발생.



2. sum_query2는 선형 루프 위주라 캐시 친화적이다

sum_query2는 대부분이 단순한 for 루프이고, 메모리를 순차적으로 접근합니다.

순차 접근은 CPU 캐시 효율이 좋기 때문에 실행 속도가 빨라집니다.

sum_query1은 앞쪽/뒤쪽에서 따로 따로 접근하다 보니, **메모리 지역성(locality)**이 떨어질 수 있습니다.


3. 컴파일러 최적화가 for 루프에 유리하다

현대 컴파일러는 for (int i = ...)처럼 명확한 루프를 더 잘 최적화합니다.

while 루프는 조건이 더 복잡하게 보이기 때문에 최적화가 덜 될 수 있어요.
```
