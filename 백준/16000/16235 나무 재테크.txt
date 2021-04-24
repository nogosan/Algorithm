#include <iostream>
#include <algorithm>
#include <queue>
#include <map>
#include <set>
#include <stack>
#include <string>
#define fastio cin.tie(0), cout.tie(0), ios::sync_with_stdio(0)
#define lint long long
#define FORI(n) for(int i = 0; i < n; i++)
#define FORJ(n) for(int j = 0; j < n; j++)
#define FORK(n) for(int k = 0; k < n; k++)
int dir[8][2] = { { 0,1 },{ 1,0 },{ 0,-1 },{ -1,0 },{1,1},{1,-1},{-1,1},{-1,-1} };

// 2021 04 25

using namespace std;

int n, m, k;
int food[10][10], winterAdding[10][10];
deque<int> trees[10][10];

void springAndSummer() {
	FORI(n) FORJ(n) {
		if (trees[i][j].empty()) continue;
		sort(trees[i][j].begin(), trees[i][j].end());
		deque<int> newTrees;
		int idx;
		// 봄: 성장
		for (idx = 0; idx < trees[i][j].size(); idx++) {
			int age = trees[i][j][idx];
			if (food[i][j] >= age) {
				food[i][j] -= age;
				newTrees.push_back(age + 1);
			}
			else break;
		}
		// 여름: 성장 못한 나무 양분이 되어
		for (; idx < trees[i][j].size(); idx++) {
			int foodFromTree = trees[i][j][idx] / 2;
			food[i][j] += foodFromTree;
		}
		
		trees[i][j] = newTrees;
	}
}

bool canGo(int y, int x) {
	if (y < 0 || y >= n || x < 0 || x >= n) return false;
	return true;
}

void fall() {
	FORI(n) FORJ(n) {
		deque<int>& trs = trees[i][j];
		for (int idx = 0; idx < trs.size(); idx++) {
			if (trs[idx] % 5 != 0) continue;
			int y = i, x = j;
			for (int d = 0; d < 8; d++) {
				int ny = y + dir[d][0], nx = x + dir[d][1];
				if (!canGo(ny, nx)) continue;
				trees[ny][nx].push_back(1);		// age 1인 나무는 번식이 불가하기 때문에 지금 단계에서 추가해도 된다.
			}
		}
	}
}

void winter() {
	FORI(n) FORJ(n) food[i][j] += winterAdding[i][j];
}

int main(void) {
	fastio;

	cin >> n >> m >> k;

	// 초기 양분 설정, 겨울에 추가되는 양분 입력 받기
	FORI(n) FORJ(n) {
		food[i][j] = 5;
		cin >> winterAdding[i][j];
	}

	// 나무의 정보 받기
	FORI(m) {
		int y, x, age;
		cin >> y >> x >> age;
		trees[y - 1][x - 1].push_back(age);
	}

	// 사계절 k번 반복
	for (int K = 0; K < k; K++) {
		// 봄과 여름
		springAndSummer();

		// 가을
		fall();

		// 겨울
		winter();
	}

	int answer = 0;
	FORI(n) FORJ(n) answer += trees[i][j].size();
	cout << answer << '\n';
}