```cpp
#if 1
#include <cstdio>
#include <vector>
#include <string>
#include <algorithm>
#include <set>
#include <unordered_map>
using namespace std;

string s;

struct Info {
	string id, t;
	vector<string> l, p;
	bool operator<(const Info& r)const {
		return t > r.t;
	}
	bool operator==(const Info& r)const {
		return id == r.id;
	}
};

struct Data {
	string id, t;
	bool operator<(const Data& r)const {
		return t > r.t;
	}
	bool operator==(const Data& r)const {
		return id == r.id;
	}
};

unordered_map<string, set<Data>> lmap, pmap;

set<Info> ss;

void daytime_edit(string& temp) {
	// 년,월,일 수정
	int first = 4, second = temp.find('/', first + 1), third = temp.find(',', second + 1);
	if (second - first == 2) temp.insert(first + 1, "0"), second++, third++;
	if (third - second == 2) temp.insert(second + 1, "0");
	temp.erase(first, 1), temp.erase(--second, 1);

	// 시간 수정
	first = 8, temp.erase(first, 1), second = temp.find(':', first), third = temp.find(':', second + 1);
	if (second - first == 1) temp.insert(first, "0"), second++, third++;
	if (third - second == 2) temp.insert(second + 1, "0"), third++;
	temp.erase(second, 1), temp.erase(--third, 1);
	if (temp.size() == 13) temp.insert(12, "0");
}

Info input_fun(char c[]) {
	string s = c, temp;
	int pos = 0, cur_pos = 0, cnt = 0, len; Info ret;
	while (pos != string::npos) {
		pos = s.find(']', cur_pos);
		len = pos - cur_pos;
		if (cnt == 0) ret.id = s.substr(cur_pos + 4, len - 4);
		if (cnt == 1) daytime_edit(ret.t = s.substr(cur_pos + 7, len - 7));
		if (cnt == 2) {
			ret.l.push_back(s.substr(cur_pos + 6, len - 6));
			lmap[ret.l.back()].insert({ ret.id,ret.t });
		}
		if (cnt == 3) {
			temp = s.substr(cur_pos + 9, len - 9);
			int pos1 = 0, cur_pos1 = 0, len1, cnt1 = 0;
			while (pos1 != string::npos) {
				pos1 = temp.find(',', cur_pos1);
				len1 = pos1 - cur_pos1;
				ret.p.push_back(temp.substr(cur_pos1, len1));
				pmap[ret.p.back()].insert({ ret.id,ret.t });
				cur_pos1 = pos1 + 1;
			}
		}
		cur_pos = pos + 1; cnt++;
	}
	return ret;
}

Info ret;
void init(int N, char pictureList[][200]) {
	ss.clear(), lmap.clear(), pmap.clear();
	for (int i = 0; i < N; i++) {
		ret = input_fun(pictureList[i]); ss.insert(ret);
	}
}

void savePictures(int M, char pictureList[][200]) {
	for (int i = 0; i < M; i++) {
		ret = input_fun(pictureList[i]); ss.insert(ret);
	}
}

int filterPictures(char mFilter[], int K) {
	string temp = mFilter;
	int s = temp.find('['), e = temp.find(']') - s - 1;
	temp.erase(0, s + 1); temp.erase(e, 1);
	bool type = false; // true: LOC, false: PEOPLE
	if (mFilter[0] == 'L') { 
		auto it = lmap[temp].begin();
		for (int i = 0; i < K - 1; i++) it++;
		return stoi(it->id);
	}
	else {
		auto it = pmap[temp].begin();
		for (int i = 0; i < K - 1; i++) it++;
		return stoi(it->id);
	}
}

int deleteOldest(void) {
	auto it = ss.rbegin();
	string ret = it->id;
	for (auto m : it->l) {
		auto it1 = lmap[m].rbegin();
		lmap[m].erase(*it1);
	}
	for (auto m : it->p) {
		auto it1 = pmap[m].rbegin();
		pmap[m].erase(*it1);
	}
	ss.erase(*it);
	return stoi(ret);
}
#endif // 0

```
