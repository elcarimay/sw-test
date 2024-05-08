```cpp
#include <cstdio>
#include <vector>
#include <string>
#include <algorithm>
#include <set>
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

set<Info> ss;

Info input_fun(char c[]) {
	string s = c, temp;
	int pos = 0, cur_pos = 0, cnt = 0, len; Info ret;
	while (pos != string::npos) {
		pos = s.find(']', cur_pos);
		len = pos - cur_pos;
		if (cnt == 0) ret.id = s.substr(cur_pos + 4, len - 4);
		if (cnt == 1) ret.t = s.substr(cur_pos + 7, len - 7);
		if (cnt == 2) ret.l.push_back(s.substr(cur_pos + 6, len - 6));
		if (cnt == 3) {
			temp = s.substr(cur_pos + 9, len - 9);
			int pos1 = 0, cur_pos1 = 0, len1, cnt1 = 0;
			while (pos1 != string::npos) {
				pos1 = temp.find(',', cur_pos1);
				len1 = pos1 - cur_pos1;
				ret.p.push_back(temp.substr(cur_pos1, len1));
				cur_pos1 = pos1 + 1;
			}
		}
		cur_pos = pos + 1; cnt++;
	}
	return ret;
}

void daytime_edit(string& temp) {
	// 년,월,일 수정
	int first = 4, second = temp.find('/', first + 1), third = temp.find(',', second + 1);
	if (second - first == 2) temp.insert(first+1, "0"), second++, third++;
	if (third - second == 2) temp.insert(second+1, "0");
	temp.erase(first, 1), temp.erase(--second, 1);

	// 시간 수정
	first = 8, temp.erase(first, 1), second = temp.find(':', first), third = temp.find(':', second + 1);
	if (second - first == 1) temp.insert(first, "0"), second++, third++;
	if (third - second == 2) temp.insert(second + 1, "0"), third++;
	temp.erase(second, 1), temp.erase(--third, 1);
	if (temp.size() == 13) temp.insert(12, "0");
}

Info ret;
void init(int N, char pictureList[][200]){
	ss.clear();
	for (int i = 0; i < N; i++) {
		ret = input_fun(pictureList[i]); daytime_edit(ret.t); ss.insert(ret);
	}
}

void savePictures(int M, char pictureList[][200]){
	for (int i = 0; i < M; i++) {
		ret = input_fun(pictureList[i]); daytime_edit(ret.t); ss.insert(ret);
	}
}

int filterPictures(char mFilter[], int K){
	string temp = mFilter;
	int s = temp.find('['), e = temp.find(']') - s-1;
	temp.erase(0, s+1); temp.erase(e, 1);
	bool type = false; // true: LOC, false: PEOPLE
	if (mFilter[0] == 'L') type = true;
	for (auto it = ss.begin(); it != ss.end(); it++) {
		if (type) { for (auto m : it->l) if (m == temp) { K--; break; } }
		else { for (auto m : it->p) if (m == temp) { K--; break; } }
		if (!K) return stoi(it->id);
	}
	return -1;
}

int deleteOldest(void){
	auto it = ss.rbegin();
	int ret = stoi(it->id);
	ss.erase(*it);
	return ret;
}
```
