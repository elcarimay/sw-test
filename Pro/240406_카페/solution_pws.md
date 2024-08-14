```cpp
#include <unordered_map>
#include <queue>
using namespace std;

#define MAX_N	10
#define MAX_ORDER	15001

int n = 0;
int order_n = 0;
int doing_order = 0;
int current_time = 0;
int remained_dish[MAX_ORDER] = { 0 };
bool done_dish[MAX_ORDER][MAX_N] = { 0 };
unordered_map<int, int> map;

struct Kitchen {
	int remained_time = 0;
	int taketime = 0;
	queue<int>	list;
}kitchen[MAX_N];

void timeticking(int mTime);

void init(int N, int mCookingTimeList[]){
	n = N;
	order_n = 0;
	doing_order = 0;
	current_time = 0;

	for (int i = 0; i < MAX_N; i++){
		kitchen[i].remained_time = 0;
		kitchen[i].taketime = mCookingTimeList[i];
		while (!kitchen[i].list.empty())
			kitchen[i].list.pop();
	}

	for (int i = 0; i < MAX_ORDER; i++){
		remained_dish[i] = 0;
		for (int j = 0; j < MAX_N; j++)
			done_dish[i][j] = false;
	}
	map.clear();
}

int order(int mTime, int mID, int M, int mDishes[]){
	timeticking(mTime - 1);
	int result = ++doing_order;
	map[mID] = order_n++;
	for (int i = 0; i < M; i++){
		int dish = mDishes[i] - 1;
		if (kitchen[dish].list.empty())
			kitchen[dish].remained_time = kitchen[dish].taketime;
		kitchen[dish].list.push(mID);
	}
	remained_dish[map[mID]] = M;
	return result;
}

int cancel(int mTime, int mID){
	timeticking(mTime - 1);

	//이미 완료된 음식 처리
	for (int i = 0; i < n; i++){
		if (done_dish[map[mID]][i] == true){
			if (!kitchen[i].list.empty()){
				//만들고 있던 다른 주문으로 완료처리
				int id = kitchen[i].list.front();
				done_dish[map[id]][i] = true;
				remained_dish[map[id]] = remained_dish[map[id]] - 1;
				if (remained_dish[map[id]] == 0)
					doing_order -= 1;
				kitchen[i].list.pop();
			}
			done_dish[map[mID]][i] = false;
		}
	}
	remained_dish[map[mID]] = 0;
	map.erase(mID);
	int result = --doing_order;
	return result;
}

int getStatus(int mTime, int mID) {
	timeticking(mTime - 1);
	int result = 0;
	if (map.find(mID) != map.end())
		result = remained_dish[map[mID]];
	else
		result = -1;
	return result;
}

void timeticking(int mTime){
	int t_passed_time = mTime - current_time;
	for (int i = 0; i < n; i++)	{
		int passed_time = t_passed_time;
		while (!kitchen[i].list.empty() && passed_time > 0){
			int id = kitchen[i].list.front();
			if (map.find(id) == map.end()) //이미 취소된 주문이라면
				kitchen[i].list.pop();
			else if (passed_time >= kitchen[i].remained_time){
				//음식 완료처리
				passed_time = passed_time - kitchen[i].remained_time;
				done_dish[map[id]][i] = true;
				remained_dish[map[id]] = remained_dish[map[id]] - 1;
				if (remained_dish[map[id]] == 0)
					doing_order -= 1;
				kitchen[i].list.pop();
				if (!kitchen[i].list.empty())
					kitchen[i].remained_time = kitchen[i].taketime;
				else
					kitchen[i].remained_time = 0;
			}
			else{ //아직 요리중이라면
				kitchen[i].remained_time = kitchen[i].remained_time - passed_time;
				passed_time = 0;
			}
		}
	}
	current_time = mTime;
}
#endif
```
