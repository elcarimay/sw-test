```cpp
# include <time.h>
# include <iostream>
# include <stdio.h>

int main() {
	clock_t start = clock();

	int a = 1, b = 2;
	for (int i = 0; i < 10; i++) {
		printf("%d %d\n", a, b);
	}
	
	int result = (clock() - start) / (CLOCKS_PER_SEC / 1000);
	printf("RESULT = %d ms\n", result);
	return 0;
}
```
