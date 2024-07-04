```cpp
#if 1
#include <vector> // Array List Library

// key와 value를 이용해서 빠르게 접근
#include <map> // Red-Black Tree(Blanced Binary Search Tree) -> LogN 동작, 메모리가 효율적
#include <unordered_map> // Hash Table -> O(1) 메모리가 많이 사용
#include <string> // 문자열 -> 종들의 이름을 입력 받기 때문
using namespace std;

const int MAX = 50000 + 1; // add가 최대 5만번, init에서 Root가 1번

#define MAXL (11) 

map<string, int> species;
int id;	// 종추가시 id로 부여

// getCount시 tree 탐색 필요 => 인접 matrix(메모리 초과, 탐색 속도 느림) or 인접 list 필요
vector<int> adj[MAX + 1];

int parent[MAX + 1];
int depth[MAX + 1];

void init(char mRootSpecies[MAXL])
{
	id = 0;
	species.clear();
	for (int i = 0; i < MAX + 1; i++)
	{
		adj[i].clear();
	}

	// Root가 되는 종 추가
	species[string(mRootSpecies)] = id; // hash는 string으로 변환해서 넣어야 함
	id++;
}

// 5만개 이하 -> 가장 빠르게
void add(char mSpecies[MAXL], char mParentSpecies[MAXL])
{
	int p = species.find(string(mParentSpecies))->second;

	// 새로운 종 추가
	int c = id++;
	species[string(mSpecies)] = c;
	depth[c] = depth[p] + 1;
	parent[c] = p;

	// 인접리스트
	adj[p].push_back(c);
	adj[c].push_back(p);
}

int getDistance(char mSpecies1[MAXL], char mSpecies2[MAXL])
{
	int u, v;
	u = species.find(string(mSpecies1))->second;
	v = species.find(string(mSpecies2))->second;

	// 둘 중 depth가 낮은 쪽과 동일할 때까지 부모 노드로 이동
	if (depth[u] < depth[v])
		swap(u, v);
	int dist = 0;
	while (depth[u] != depth[v]) {
		u = parent[u];
		dist++;
	}

	// 둘이 동일한 노드가 되면 종료
	if (u == v) return dist;

	// 둘이 동일한 부모 노드를 만날때까지 반복해서 부모 노드로 이동
	while (u != v) {
		u = parent[u];
		v = parent[v];
		dist += 2;
	}

	return dist;
}

/*
	AD: dfs, bfs => 그래프와 트리에서 탐색을 하는 알고리즘
	~ dfs(depth fist search): 깊이를 우선으로 하는 알고리즘, 재귀함수
	~ bfs(breath first search): 너비를 우선으로 탐색하는 알고리즘, 큐사용

	=> 전체 최대 mDistance까지 탐색을 해야 하기 때문에 구현하기 편한 것을 선택
*/

// p: 이전에 왔던 부모 노드, cur: 현재 노드, 현재 distance
int dfs(int p, int cur, int distance) {
	// 더 이상 쪼개지지 않는 케이스: 기저 사례(Base case)
	if (distance == 0)
		return 1;

	int ret = 0;
	for (auto next: adj[cur])
	{
		// 탐색했었던 부모를 다시 탐색 x
		if (next == p)	continue;
		
		// 더 탐색한 결과를 더해서 리턴
		ret += dfs(cur, next, distance - 1);
	}
	return ret;
}

int getCount(char mSpecies[MAXL], int mDistance)
{
	int u = species.find(string(mSpecies))->second;
	
	return dfs(-1, u, mDistance);
}
#endif
```
