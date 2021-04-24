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
int dir[4][2] = { { 0,1 },{ 1,0 },{ 0,-1 },{ -1,0 }};		// 우 하 좌 상 -> L : -1, D: +1

// 2021 04 25

using namespace std;

typedef struct {
	int sec;
	char direction;
} turn;

int n, k, l;
bool apple[100][100] = { false, };
turn turnInfo[100] = { 0, };

deque<pair<int, int>> pos;		// 머리부터 순서대로, 뱀의 모든 신체의 위치
int hy = 0, hx = 0, d = 0;		// 뱀의 머리 위치, 방향 정보
int ty = 0, tx = 0;					// 뱀의 꼬리 위치

bool isIn(int y, int x) {
	if (y < 0 || y >= n || x < 0 || x >= n) return false;
	return true;
}

bool headOnBody(int y, int x) {		// 자기 몸에 부딪힌 뱀 ㅜ_ㅜ
	if (y == ty && x == tx) return true;
	FORI(pos.size())
		if (pos[i].first == y && pos[i].second == x) return true;
	return false;
}

bool gameOver(int y, int x) {
	if (!isIn(y, x) || headOnBody(y, x)) return true;
	return false;
}

int main(void) {
	fastio;

	cin >> n >> k;
	// get apples' position
	FORI(k) {
		int y, x;
		cin >> y >> x;
		apple[y - 1][x - 1] = true;
	}
	// get move information
	cin >> l;
	FORI(l)
		cin >> turnInfo[i].sec >> turnInfo[i].direction;

	int sec = 0;
	int turnIdx = 0;
	while (true) {
		sec++;
		// 몸 늘리기
		hy += dir[d][0], hx += dir[d][1];
		// 게임 오버인가?
		if (gameOver(hy, hx)) {
			cout << sec << '\n';
			return 0;
		}
		// 게임 오버 아니면, 머리 위치를 pos에 추가
		pos.push_back({ hy, hx });
		// 사과 있으면 먹어라
		if (apple[hy][hx])
			apple[hy][hx] = false;
		else {
			ty = pos.front().first, tx = pos.front().second;
			pos.pop_front();
		}
		// 방향 변경해야 하는지 확인
		if (turnIdx >= l) continue;
		int theTime = turnInfo[turnIdx].sec;
		if (sec == theTime) {
			char way = turnInfo[turnIdx].direction;
			if (way == 'D') d = (d + 1) % 4;
			else if (way == 'L') d = (d - 1 + 4) % 4;
			turnIdx++;
		}
	}
}