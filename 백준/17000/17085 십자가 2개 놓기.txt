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

int n, m, answer = 1;
char v[15][15];
bool visited[15][15] = { 0, };

bool canGo(int y, int x) {
	if (y < 0 || y >= n || x < 0 || x >= m) return false;
	if (visited[y][x] || v[y][x] == '.') return false;
	return true;
}

bool isCross(int y, int x, int len) {
	if (v[y][x] == '.') return false;
	queue<pair<int, int>> q;
	FORI(4) q.push({ y, x });
	FORI(len) {
		FORJ(4) {
			int ny = q.front().first + dir[j][0], nx = q.front().second + dir[j][1];
			q.pop();
			if (!canGo(ny, nx)) return false;
			q.push({ ny, nx });
		}
	}
	return true;
}

void changeVisited(int y, int x, int len, bool content) {
	visited[y][x] = content;
	queue<pair<int, int>> q;
	FORI(4) q.push({ y, x });
	FORI(len) {
		FORJ(4) {
			int ny = q.front().first + dir[j][0], nx = q.front().second + dir[j][1];
			q.pop();
			visited[ny][nx] = content;
			q.push({ ny, nx });
		}
	}
}

void findSecondCross(int y, int x, int firstCrossLen) {
	int maxLen = min(n, m) / 2;
	for (int i = y; i < n; i++)
		for (int j = 0; j < m; j++) {
			if (i == y && j <= x) continue;
			for (int len = 1; len <= maxLen; len++)
				if (isCross(i, j, len))
					answer = max(answer, (1 + 4 * firstCrossLen) * (1 + 4 * len));
		}
}

void findCross() {
	int maxLen = min(n, m) / 2;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			for (int len = 1; len <= maxLen; len++)		 // len means cross wing length except center
				if (isCross(i, j, len)) {
					answer = max(answer, (1 + 4 * len));
					changeVisited(i, j, len, true);
					findSecondCross(i, j, len);
					changeVisited(i, j, len, false);
				}
}

int main(void) {
	fastio;

	cin >> n >> m;
	FORI(n) FORJ(m) cin >> v[i][j];
	findCross();
	cout << answer << '\n';
}