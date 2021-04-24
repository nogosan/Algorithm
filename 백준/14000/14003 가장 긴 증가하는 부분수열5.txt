#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
#include <string>
#include <map>
#include <queue>
#include <stack>
#include <set>
#include <ctime>
#define fastio ios::sync_with_stdio(0), cin.tie(0), cout.tie(0)
#define lint long long
#define FORI(n) for(int i = 0; i < n; i++)
#define FORJ(n) for(int j = 0; j < n; j++)
#define FORK(n) for(int k = 0; k < n; k++)
int dir[4][2] = { {0,1},{1,0},{0,-1},{-1,0} };

// 2021 04 01

using namespace std;

const int MAX = 1000001;

int n, m, answer = 0;
pair<int, int> v[MAX];		// (value, LIS에서 왼쪽 값의 index(제일왼쪽이면 0))
vector<pair<int, int>> LIS;

void setPos(int left, int right, int value, int& pos) {
	if (left > right) return;
	int middle = (left + right) / 2;
	int middleValue = LIS[middle].first;
	if (middleValue == value) {
		pos = -2;
		return;
	}
	if (middleValue > value) {
		pos = middle;
		setPos(left, middle - 1, value, pos);
	}
	else
		setPos(middle + 1, right, value, pos);
}

int main(void) {
	fastio;

	cin >> n;
	for (int i = 1; i <= n; i++) cin >> v[i].first;
	LIS.push_back({ v[1].first, 1 });
	for (int i = 2; i <= n; i++) {
		int nextValue = v[i].first;
		int backValue = LIS.back().first;
		if (nextValue > backValue) {
			v[i].second = LIS.back().second;
			LIS.push_back({ nextValue, i });
		}
		else if (nextValue == backValue)
			continue;
		else {
			int pos = -1;
			setPos(0, LIS.size() - 1, nextValue, pos);
			if (pos >= 0) {
				LIS[pos] = { nextValue, i };
				if (pos != 0) v[i].second = LIS[pos - 1].second;
			}
			else if (pos != -2)
				LIS[0] = { nextValue, i };
		}
	}

	vector<int> answer;
	pair<int, int> back = LIS.back();
	answer.push_back(back.first);
	while (true) {
		int vPos = back.second;
		int nextPos = v[vPos].second;
		if (nextPos == 0) break;
		answer.push_back(v[nextPos].first);
		back.second = nextPos;
	}
	reverse(answer.begin(), answer.end());
	cout << answer.size() << '\n';
	FORI(answer.size()) cout << answer[i] << ' ';
	cout << '\n';
}