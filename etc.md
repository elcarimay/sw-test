<C++>
디버그시 조사식에 변수이름을 입력하여 실시간으로 모니터링 가능
하기 코드에서 for문 앞에 중단점을 걸고 debug실행해서 _CrtDumpMemoryLeaks_ 변수가 생기는지 확인하고
생겼다면 Memoryleak발생한 것임.

```C++
//아래 두줄 입력
#define _CRTDBG_MAP_ALLOC
#include <crtdbg.h>

int main()
{
    int* pNum = (int*)malloc(sizeof(int) * 10);
    for (int i = 0; i < 10; i++)
    {
        *(pNum + 1) = i;
    }
    // 프로그램이 끝나기전에 아래 한줄 입력
    _CrtDumpMemoryLeaks();

    return 0;
}
```
