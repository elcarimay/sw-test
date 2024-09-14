```cpp
struct PriorityQueue {
    struct Data {
        int id;
        unsigned long long value;
    };
 
    Data heap[MAX_SIZE];
    int heapSize = 0;
 
    void heapInit() {
        heapSize = 0;
    }
 
    void heapPush(int id, unsigned long long value) {
        heap[heapSize].id = id;
        heap[heapSize].value = value;
 
        int current = heapSize;
 
        while (current > 0 && heap[current].value < heap[(current - 1) / 2].value) {
            Data temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
 
        heapSize = heapSize + 1;
    }
 
    Data heapPop() {
        Data value = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
 
        int current = 0;
 
        while (current * 2 + 1 < heapSize) {
            int child;
 
            if (current * 2 + 2 == heapSize)
                child = current * 2 + 1;
            else
                child = heap[current * 2 + 1].value < heap[current * 2 + 2].value ? current * 2 + 1 : current * 2 + 2;
 
            if (heap[current].value < heap[child].value)
                break;
 
            Data temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return value;
    }
};
```
