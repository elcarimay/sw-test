## 배열 인덱스로 인코딩
```cpp
#include <vector>
#include <queue>
using namespace std;

#define MAX_HOTELS  1'001            
#define MAX_ROOMS   100'001        

// hotels
vector<int> roomList[MAX_HOTELS];

// rooms
struct Room {
    int price, mRoomID;

    bool operator<(const Room& room) const {
        return (price > room.price) || (price == room.price && mRoomID > room.mRoomID);
    } 
} rooms[MAX_ROOMS];
int roomCnt;

struct RoomInfo {
    int regionNo, numBeds, roomType, viewType;
} roomInfo[MAX_ROOMS];

struct Date { int checkIn, checkOut; };
vector<Date> booking[MAX_ROOMS];

// encoding
int encode(const RoomInfo& info) {
    return (((info.regionNo - 1) * 10 + (info.numBeds - 1)) * 10 + info.roomType) * 10 + info.viewType;
}
priority_queue<Room> roomMap[10'000];   // roomMap[0 ~ 9'999]

bool check_booking(int mRoomID, int checkIn, int checkOut) {
    for (const auto& date : booking[mRoomID])
        if (checkOut > date.checkIn && date.checkOut > checkIn)
            return false;
    return true;
}

//////////////////////////////////////////////////////////////////////
void init(int N, int mRoomCnt[])
{
    for (int i = 0; i < roomCnt; i++) booking[i].clear();
    for (int i = 1; i <= N; i++) roomList[i].clear();
    for (int i = 0; i < 10'000; i++) roomMap[i] = {};
    roomCnt = 0;
}

void addRoom(int mHotelID, int mRoomID, int mRoomInfo[])
{
    roomCnt++;
    roomList[mHotelID].push_back(mRoomID);
    rooms[mRoomID] = { mRoomInfo[4], mRoomID };
    roomInfo[mRoomID] = { mRoomInfo[0], mRoomInfo[1], mRoomInfo[2], mRoomInfo[3] };
    roomMap[encode(roomInfo[mRoomID])].push(rooms[mRoomID]);
}

int findRoom(int mFilter[])
{
    int checkIn = mFilter[0], checkOut = mFilter[1];
    RoomInfo info = { mFilter[2], mFilter[3], mFilter[4], mFilter[5] };
    auto& pq = roomMap[encode(info)];
    vector<int> popped;

    int res = -1;
    while (!pq.empty()) {
        auto room = pq.top(); pq.pop();
        int mRoomID = room.mRoomID;

        if (room.price != rooms[mRoomID].price) continue;

        popped.push_back(mRoomID);
        if (check_booking(mRoomID, checkIn, checkOut)) {
            booking[mRoomID].push_back({ checkIn, checkOut });
            res = mRoomID;
            break;
        }
    }
    for (int mRoomID : popped) pq.push(rooms[mRoomID]);
    return res;
}

int riseCosts(int mHotelID)
{
    int res = 0;
    for (int mRoomID : roomList[mHotelID]) {
        rooms[mRoomID].price *= 1.1;
        res += rooms[mRoomID].price;
        roomMap[encode(roomInfo[mRoomID])].push(rooms[mRoomID]);
    }
    return res;
}
```
## 사용자 정의 해시 함수
```cpp
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

#define MAX_HOTELS  1'001            
#define MAX_ROOMS   100'001        

// hotels
vector<int> roomList[MAX_HOTELS];

// rooms
struct Room {
    int price, mRoomID;

    bool operator<(const Room& room) const {
        return (price > room.price) || (price == room.price && mRoomID > room.mRoomID);
    }
} rooms[MAX_ROOMS];
int roomCnt;

struct RoomInfo {
    int regionNo, numBeds, roomType, viewType;
    
    bool operator==(const RoomInfo& info) const {
        return regionNo == info.regionNo && numBeds == info.numBeds &&
               roomType == info.roomType && viewType == info.viewType;
    }
} roomInfo[MAX_ROOMS];

struct Date { int checkIn, checkOut; };
vector<Date> booking[MAX_ROOMS];

// user hash function (STL)
template<>
struct hash<RoomInfo> {
    size_t operator()(const RoomInfo& info) const {
        return hash<int>()(info.regionNo) ^ hash<int>()(info.numBeds) ^
               hash<int>()(info.roomType) ^ hash<int>()(info.viewType);
    }
};
unordered_map<RoomInfo, priority_queue<Room>> roomMap;

bool check_booking(int mRoomID, int checkIn, int checkOut) {
    for (const auto& date : booking[mRoomID])
        if (checkOut > date.checkIn && date.checkOut > checkIn)
            return false;
    return true;
}

//////////////////////////////////////////////////////////////////////
void init(int N, int mRoomCnt[])
{
    for (int i = 0; i < roomCnt; i++) booking[i].clear();
    for (int i = 1; i <= N; i++) roomList[i].clear();
    roomMap.clear();
    roomCnt = 0;
}

void addRoom(int mHotelID, int mRoomID, int mRoomInfo[])
{
    roomCnt++;
    roomList[mHotelID].push_back(mRoomID);
    rooms[mRoomID] = { mRoomInfo[4], mRoomID };
    roomInfo[mRoomID] = { mRoomInfo[0], mRoomInfo[1], mRoomInfo[2], mRoomInfo[3] };
    roomMap[roomInfo[mRoomID]].push(rooms[mRoomID]);
}

int findRoom(int mFilter[])
{
    int checkIn = mFilter[0], checkOut = mFilter[1];
    RoomInfo info = { mFilter[2], mFilter[3], mFilter[4], mFilter[5] };
    auto& pq = roomMap[info];
    vector<int> popped;

    int res = -1;
    while (!pq.empty()) {
        auto room = pq.top(); pq.pop();
        int mRoomID = room.mRoomID;

        if (room.price != rooms[mRoomID].price) continue;

        popped.push_back(mRoomID);
        if (check_booking(mRoomID, checkIn, checkOut)) {
            booking[mRoomID].push_back({ checkIn, checkOut });
            res = mRoomID;
            break;
        }
    }
    for (int mRoomID : popped) pq.push(rooms[mRoomID]);
    return res;
}

int riseCosts(int mHotelID)
{
    int res = 0;
    for (int mRoomID : roomList[mHotelID]) {
        rooms[mRoomID].price *= 1.1;
        res += rooms[mRoomID].price;
        roomMap[roomInfo[mRoomID]].push(rooms[mRoomID]);
    }
    return res;
}
```
