```cpp
void build(int a[]) {
	A = a;
	for (sq = 1; sq * sq < N; sq++);
	for (int i = 0; i < N; i++) {
		int bid = i / sq;
		if (bid * sq == i) minA[bid] = INF, maxA[bid] = -INF, sumA[bid] = 0;
		minA[bid] = min(minA[bid], A[i]), maxA[bid] = max(maxA[bid], A[i]);
		sumA[bid] += A[i];
	}
}

void update(int idx, int value) {
	int orgVal = A[idx];
	A[idx] += value;
	int bid = idx / sq;
	int l = bid * sq;
	int r = min(l + sq, N);
	if (value < 0) {
		if (maxA[bid] == orgVal) maxA[bid] = *max_element(A + l, A + r);
		minA[bid] = min(minA[bid], A[idx]);
	}
	else {
		maxA[bid] = max(maxA[bid], A[idx]);
		if (minA[bid] == orgVal) minA[bid] = *min_element(A + l, A + r);
	}
	sumA[bid] += value;
}

int sum_query(int l, int r) {
	int sumv = 0;
	int sbid = l / sq, ebid = r / sq;
	if (sbid == ebid) {
		for (int i = l; i <= r; i++) sumv += A[i];
		return sumv;
	}
	for (int i = l; i <= (sbid + 1) * sq - 1; i++) sumv += A[i];
	for (int i = sbid + 1; i <= ebid - 1; i++) sumv += sumA[i];
	for (int i = ebid * sq; i <= r; i++) sumv += A[i];
	return sumv;
}

int maxmin_query(int l, int r) {
	int maxv = -INF, minv = INF;
	int sbid = l / sq, ebid = r / sq;
	if (sbid == ebid) {
		for (int i = l; i <= r; i++) maxv = max(maxv, A[i]), minv = min(minv, A[i]);
		return maxv - minv;
	}
	for (int i = l; i <= (sbid + 1) * sq - 1; i++) maxv = max(maxv, A[i]), minv = min(minv, A[i]);
	for (int i = ebid * sq; i <= r; i++) maxv = max(maxv, A[i]), minv = min(minv, A[i]);
	for (int i = sbid + 1; i <= ebid - 1; i++) maxv = max(maxv, maxA[i]), minv = min(minv, minA[i]);
	return maxv - minv;
}
```
