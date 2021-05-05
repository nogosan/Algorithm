#include <iostream>
#include <algorithm>
#include <queue>
#include <map>
#include <set>
#include <stack>
#include <string>
#include <cmath>
#define fastio cin.tie(0), cout.tie(0), ios::sync_with_stdio(0)
#define lint long long
#define FORI(n) for(int i = 0; i < n; i++)
#define FORJ(n) for(int j = 0; j < n; j++)
#define FORK(n) for(int k = 0; k < n; k++)
int dir[4][2] = { { 0,1 },{ 1,0 },{ 0,-1 },{ -1,0 } };		// 우 하 좌 상

// 2021 05 05

using namespace std;

const int DRAW = 100;
const int ERASE = -100;

int n, m, answer = 2147000000;
vector<vector<int>> v;

bool isCamera(int num) {
	return (1 <= num && num <= 5);
}

class camera {
private:
	int kind, lines;
	int y, x;
	bool counterCamera;													// if it is camera 2
	bool isReachable(int i, int j) {
		if (i < 0 || i >= n || j < 0 || j >= m) return false;
		if (v[i][j] == 6) return false;
		return true;
	}
	void drawOneLine(int action, int offsetY, int offsetX) {		// subfuction of draw
		int ny = y, nx = x;
		while (isReachable(ny + offsetY, nx + offsetX)) {
			ny += offsetY;
			nx += offsetX;
			if (isCamera(v[ny][nx])) continue;
			v[ny][nx] += action;
		}
	}
public:
	camera(int cameraKind, int i, int j) {
		kind = cameraKind;
		y = i; x = j;
		counterCamera = (cameraKind == 2);
		if (!counterCamera) {
			lines = kind;
			if (lines > 1) lines--;
		}
		else lines = -1;														// if countercamera, useless
	}
	void draw(int action, int direction) {
		if (counterCamera) {
			int d1 = direction, d2 = (direction + 2) % 4;
			drawOneLine(action, dir[d1][0], dir[d1][1]);
			drawOneLine(action, dir[d2][0], dir[d2][1]);
		}
		else {
			FORI(lines) {
				int d = (direction + i) % 4;
				drawOneLine(action, dir[d][0], dir[d][1]);
			}
		}
	}
};
vector<camera> cameras;

void getInput() {
	cin >> n >> m;
	v.resize(n);
	FORI(n) v[i].resize(m);

	FORI(n) FORJ(m) {
		cin >> v[i][j];
		if (isCamera(v[i][j])) {
			camera newCamera(v[i][j], i, j);
			cameras.push_back(newCamera);
		}
	}
}

int countBlindSpot() {
	int cnt = 0;
	FORI(n) FORJ(m)
		if (v[i][j] == 0) cnt++;
	return cnt;
}

void permu(int count, int wholeCount, int nextCamera) {
	if (count == wholeCount)
		answer = min(answer, countBlindSpot());
	else {
		for (int i = nextCamera; i < cameras.size(); i++) {
			camera& cam = cameras[i];
			FORJ(4) {
				cam.draw(DRAW, j);
				permu(count + 1, wholeCount, i + 1);
				cam.draw(ERASE, j);
			}
		}
	}
}

int main(void) {
	fastio;

	getInput();
	permu(0, cameras.size(), 0);
	cout << answer << '\n';
}