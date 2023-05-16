```cpp
#include <queue>
#include <iostream>
using namespace std;

struct Data
{
    int index;
    int value;
};

struct Comparator
{
    // 인자 o2를 기준으로 비교기준을 모두 true로 설정
    // 아래는 value가 큰 값부터 나오되, 값이 동일하다면 index가 작은 것부터 나오도록 하는 정렬자
    bool operator()(const Data& o1, const Data& o2) {
        if (o1.value < o2.value) return true;
        else if (o1.value > o2.value) return false;
        else { // o1.value == o2.value
            if (o1.index > o2.index) return true;
            else return false;
        }
    }
};

int main() {
    //선언
    priority_queue<Data, vector<Data>, Comparator> q;

    // 초기화(clear)
    // queue가 비어있지 않다면 1개씩 버린다 => queue가 텅 빌 때까지 1개씩 버림
    while (q.empty() == false) q.pop();
    //while (q.size()) q.pop();

    // 요소 삽입(1)
    // Data 객체를 임시 생성한 후에 삽입하는 방식(임시생성이므로 휘발성이며, Data변수는 사라짐)
    q.push(Data{ 30,100 });
    q.push(Data{ 20,100 });
    q.push(Data{ 10,100 });
    q.push(Data{ 40,100 });
    q.push(Data{ 50,100 });

    // 요소 삽입(2)
    Data d1 = { 60,100 };
    q.push(d1); // Data객체를 생성한 후에 Data변수를 그대로 삽입하는 방식
    cout << q.top().index << " " << q.top().value << endl << endl;

    // 최우선값 가져오기
    Data d = q.top(); // queue의 가장 앞에 있는 요소는 사라지지 않음.
    q.pop(); // 최우선값 버리기
    cout << q.top().index << " " << q.top().value << endl << endl;

    int cnt = q.size();
    for (int i = 0; i<cnt; i++) {
        if (q.empty()) break;
        cout << q.top().index << " " << q.top().value << endl;
        q.pop();

    }
    // printf("index : value");
    // printf("   %d :    %d", d.index, d.value);
    return 0;
}

//Output
//10 100
//
//20 100
//
//20 100
//30 100
//40 100
//50 100
//60 100
```
