```cpp
#if 1
#include<unordered_map>
#include<set>
#include<queue>
#include<string.h>
using namespace std;

const int LM = 20003;
int hcnt, tick, totSize;

struct Access { int id, tick; };
struct Range {
	int s, e;
	bool operator<(const Range& r) const { return s < r.s; }
};
struct File {
	int s, e, tick;
}file[LM];

set<Range> emptyMem;
queue<Access> accessLog;
unordered_map<int, int> htab;

void init(int n) {
	htab.clear();
	totSize = tick = hcnt = 0;
	emptyMem = { { 0, n } };
	accessLog = {};
	memset(file, 0, sizeof(file));
}

int access(int dataID, int dataSize) {
	int id = htab[dataID];
	if (!id) htab[dataID] = id = ++hcnt;

	if (!file[id].tick) {
		totSize += dataSize;

		auto it = emptyMem.begin();
		for (; it != emptyMem.end(); ++it)
			if (it->e - it->s >= dataSize) break;

		if (it == emptyMem.end()) {
			while (1) {
				auto t = accessLog.front();
				accessLog.pop();
				if (file[t.id].tick != t.tick) continue;
				file[t.id].tick = 0;
				int s = file[t.id].s, e = file[t.id].e;
				totSize -= e - s;
				it = emptyMem.lower_bound({ s, 0 });
				if (it != emptyMem.end() && e == it->s) e = it->e, it = emptyMem.erase(it);
				if (it != emptyMem.begin() && (--it)->e == s) s = it->s, emptyMem.erase(it);
				it = emptyMem.insert({ s, e }).first;
				if (e - s >= dataSize) break;
			}
		}
		file[id] = { it->s, it->s + dataSize };
		if (it->e - it->s > dataSize) emptyMem.insert({ it->s + dataSize, it->e });
		emptyMem.erase(it);
	}
	file[id].tick = ++tick;
	accessLog.push({ id, tick });
	return file[id].s;
}

int usage() {
	return totSize;
}

#endif // 0

```
