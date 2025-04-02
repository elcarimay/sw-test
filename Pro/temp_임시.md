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
```
