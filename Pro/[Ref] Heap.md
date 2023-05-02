
```C++
#include <stdio.h>

#define MAX_SIZE 100

struct node
{
	int idx;	// 실제 데이터가 있는 배열의 idx
	int value;	// 우선순위 가중치
};

struct PQ
{
	node heap[MAX_SIZE];
	int heapSize = 0;

	void heapInit(void)
	{
		heapSize = 0;
	}

	int heapPush(int idx, int value)
	{
		heap[heapSize].value = value;
		heap[heapSize].idx = idx;

		int current = heapSize;
		while (current > 0 && heap[current].value > heap[(current - 1) / 2].value)
		{
			node temp = heap[(current - 1) / 2];
			heap[(current - 1) / 2] = heap[current];
			heap[current] = temp;

			current = (current - 1) / 2;
		}

		heapSize = heapSize + 1;

		return 1;
	}
	node heapPop()
	{
		node tmp = heap[0];
		heapSize = heapSize - 1;
		heap[0] = heap[heapSize];

		int current = 0;

		while (current * 2 + 1 < heapSize)
		{
			int child;
			if (current * 2 + 2 == heapSize)
			{
				child = current * 2 + 1;
			}
			else
			{
				child = heap[current * 2 + 1].value > heap[current * 2 + 2].value ? current * 2 + 1 : current * 2 + 2;
			}
			if (heap[current].value > heap[child].value)
			{
				break;
			}

			node temp = heap[current];
			heap[current] = heap[child];
			heap[child] = temp;
			current = child;

		}

		return tmp;
	}
	void heapModify()
	{

	}

};

```
