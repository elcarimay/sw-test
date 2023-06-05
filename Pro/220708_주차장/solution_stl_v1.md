```cpp
//////////////////////////////////////////////////////////////////////////////////////////
//1. 자동차의 정보를 배열에 담아 관리함
//
//2. 신규 자동차가 온 경우, 배열에 추가하면서 unordered_map(hashmap)에 담아서 
//   배열에서 자동차 위치를 바로 찾을 수 있도록 함
//
//3. 대기순서에 넣어야 하는 경우(주차장이 다 찬 경우) 우선순위 큐에 넣음 
//   나중에 꺼내쓸 때 유효값인지 체크 하기 위해 등록시점인 mTime도 같이 넣어줌
//
//4. 우선순위 큐는 아래의 우선순위로 지정함
//   전체시간 - 도착시간 - 과거 총주차시간 + 과거 총대기시간
//
//5. 우선순위 큐에서 찾을때, 배열의 mTime과 큐의 mTime를 비교하여 유효값인지 체크
//   배열의 자동차가 현재 대기상태인지 체크함
//
//////////////////////////////////////////////////////////////////////////////////////////

#define MAX_CAR 100000
#define MAX_TIME 300000
#define LEFT 0
#define PARKING 1
#define WAITING 2
#include <queue>
#include <unordered_map>
using namespace std;

int BaseTime;
int BaseFee;
int UnitTime;
int UnitFee;
int Capacity;

struct waitingCar {
    int CarNo;
    int InputTime;
    int totalParkingTime;
    int totalWaitingTime;
};

struct wait_comp {
    bool operator()(const waitingCar& before, const waitingCar& after) {
        // 전체시간 - 도착시간 - 과거 총주차시간 + 과거 총대기시간
        int before_time = MAX_TIME - before.InputTime - before.totalParkingTime + before.totalWaitingTime;
        int after_time = MAX_TIME - after.InputTime - after.totalParkingTime + after.totalWaitingTime;
        if (before_time < after_time) return true;
        else if (before_time == after_time && before.InputTime > after.InputTime) return true;
        else return false;
    }
};

priority_queue<waitingCar, vector<waitingCar>, wait_comp> wQueue;

struct Car {
    int CarNo;
    int InputTime;
    int totalParkingTime;
    int totalWaitingTime;
    int status; // LEFT(0), PARKING(1), WAITING(2)
};

Car car[MAX_CAR];
int cwp;

unordered_map<int, int> map;

int pwp; // 주차장변수
int wwp; // 대기열변수

int CheckPrice(int toTime, int fromTime) { // Ceil연산자로 수정가능
    int diffTime = toTime - fromTime;
    if (diffTime <= BaseTime) return BaseFee;
    else {
        //남은거 체크해야 함
        int unitCnt = (diffTime - BaseTime) / UnitTime;
        if ((diffTime - BaseTime) % UnitTime > 0) unitCnt++;
        return BaseFee + unitCnt * UnitFee;
    }
}

void init(int mBaseTime, int mBaseFee, int mUnitTime, int mUintFee, int mCapacity) {
    // 초기변수 받기
    BaseTime = mBaseTime;
    BaseFee = mBaseFee;
    UnitTime = mUnitTime;
    UnitFee = mUintFee;
    Capacity = mCapacity;

    // 초기화 하기
    map.clear();
    cwp = 0;
    pwp = 0;
    wwp = 0;

    // 대기열 비우기
    while (wQueue.empty() == false) wQueue.pop();

    return;
}

int arrive(int mTime, int mCar) {
    // 차의 정보를 Map과 배열에 저장

    // 처음 온차 등록, 중복된차면 pass
    if (map.count(mCar) == 0) {
        map.insert({ mCar, cwp });
        car[cwp] = { mCar, mTime, 0, 0, LEFT };
        cwp++;
    }

    // 주차장 비었는지 체크
    if (Capacity > pwp) {
        int cIdx = map[mCar];

        car[cIdx].InputTime = mTime;
        car[cIdx].status = PARKING;
        pwp++;
    }
    else { // 주차장 가득 참
        int cIdx = map[mCar];
        car[cIdx].InputTime = mTime;
        car[cIdx].status = WAITING;
        wQueue.push({ mCar, mTime, car[cIdx].totalParkingTime, car[cIdx].totalWaitingTime });
        wwp++;
    }
    return wwp; // 대기차량 갯수 반환
}

int leave(int mTime, int mCar) {
    int cIdx = map[mCar];

    // 차량이 주차장에 있는 경우
    if (car[cIdx].status == PARKING) {
        //가격 추출
        int result = CheckPrice(mTime, car[cIdx].InputTime);
        //차량정보 업데이트
        car[cIdx].totalParkingTime += (mTime - car[cIdx].InputTime);
        car[cIdx].InputTime = mTime; // 현재시간으로 변경
        car[cIdx].status = LEFT;

        if (wwp > 0) {
            while (!wQueue.empty()) {
                int CarNo = wQueue.top().CarNo;
                int inputTime = wQueue.top().InputTime;

                wQueue.pop();
                int widx = map[CarNo];

                // 차량이 대기중이고 큐에 담긴 시점이후에 차가 이동하지 않은 경우
                if (car[widx].status == WAITING && car[widx].InputTime <= inputTime) {
                    car[widx].status = PARKING;
                    car[widx].totalWaitingTime += (mTime - car[widx].InputTime);
                    car[widx].InputTime = mTime;
                    wwp--;
                    break;
                }
            }
        }
        else pwp--;
        return result;
    }
    else{ // 대기차량 일때
        car[cIdx].totalWaitingTime += (mTime - car[cIdx].InputTime);
        car[cIdx].InputTime = mTime;
        car[cIdx].status = LEFT;
        wwp--;
        return -1;
    }
}
```
