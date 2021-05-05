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
int dir[4][2] = { { 0,1 },{ 1,0 },{ 0,-1 },{ -1,0 } };		// 우 하 좌 상

// 2021 04 26

using namespace std;

int n, m, moveCnt = 0;
vector<vector<char>> input;
string inputString;
vector<vector<char>> pruning, gameOver;
set<string> visited;

map<string, string> footStep;		// to, from
map<string, int> footDir;			// to, dir (우 하 좌 상)
void makeFootStep(string from, string to, int d) {
	footStep[from] = to;
	footDir[from] = d;
}
char toDir(int d) {
	if (d == 0) return 'R';
	else if (d == 1) return 'D';
	else if (d == 2) return 'L';
	else if (d == 3) return 'U';
}
void printFootStep(string end) {
	deque<int> d;
	while (end != inputString) {
		d.push_front(footDir[end]);
		end = footStep[end];
	}
	cout << d.size() << '\n';
	FORI(d.size()) cout << toDir(d[i]);
	cout << '\n';
}

// visited part
string toString(vector<vector<char>>& v) {
	string st;
	FORI(n) FORJ(m) st.push_back(v[i][j]);
	return st;
}
bool checkVisited(vector<vector<char>>& v) {
	string st = toString(v);
	if (visited.find(st) != visited.end()) return true;
	else return false;
}
void setVisited(vector<vector<char>>& v) {
	string st = toString(v);
	visited.insert(st);
}

// move part
bool canGo(int y, int x, vector<vector<char>>& v) {
	if (y < 0 || y >= n || x < 0 || x >= m) return false;
	if (v[y][x] != '.') return false;
	return true;
}
void findBallPosition(int& y, int& x, char ball, vector<vector<char>>& v) {
	FORI(n) FORJ(m)
		if (v[i][j] == ball) {
			y = i;
			x = j;
			return;
		}
}
bool isExit(int y, int x, vector<vector<char>>& v) {
	if (y < 0 || y >= n || x < 0 || x >= m) return false;
	if (v[y][x] == 'O') return true;
	else return false;
}
void move(int fy, int fx, int ty, int tx, vector<vector<char>>& v) {
	swap(v[fy][fx], v[ty][tx]);
}
void setPos(int& fy, int& fx, int& ty, int& tx) {
	swap(fy, ty);
	swap(fx, tx);
}
void ifCanGoThenMove(int& fy, int& fx, int& ty, int& tx, vector<vector<char>>& v, bool& movedFlag) {
	if (canGo(ty, tx, v)) {
		move(fy, fx, ty, tx, v);
		setPos(fy, fx, ty, tx);
		movedFlag = true;
	}
}
void keepMovingUntilWallOrExit(int& fy, int& fx, int& ty, int& tx, vector<vector<char>>& v, bool& movedFlag, bool& gotOutFlag) {
	if (gotOutFlag) { /* do nothing */ }
	else if (isExit(ty, tx, v)) {
		gotOutFlag = true;
		v[fy][fx] = '.';
	}
	else ifCanGoThenMove(fy, fx, ty, tx, v, movedFlag);
}
vector<vector<char>> moveWithDir(vector<vector<char>> v, int dirIdx, bool& GAMEOVER) {
	int ry, rx, by, bx;
	findBallPosition(ry, rx, 'R', v);
	findBallPosition(by, bx, 'B', v);
	int yOffset = dir[dirIdx][0], xOffset = dir[dirIdx][1];
	bool redGotOut = false, blueGotOut = false;
	while (true) {
		bool redMoved = false, blueMoved = false;
		int nry = ry + yOffset, nrx = rx + xOffset;
		int nby = by + yOffset, nbx = bx + xOffset;
		// keep moving until it is the WALL or the EXIT
		keepMovingUntilWallOrExit(ry, rx, nry, nrx, v, redMoved, redGotOut);
		keepMovingUntilWallOrExit(by, bx, nby, nbx, v, blueMoved, blueGotOut);
		// until both balls stop moving
		if (!redMoved && !blueMoved) {
			// no ball Exit -> keep BFS
			if (!redGotOut && !blueGotOut) return v;
			// blue Exit -> pruning
			else if (!redGotOut && blueGotOut) return pruning;
			// red & blue Exit -> pruning
			else if (redGotOut && blueGotOut) return pruning;
			// red Exit -> FOUND ANSWER
			else if (redGotOut && !blueGotOut) {
				GAMEOVER = true;
				return v;
			}
		}
	}
}

// BFS
void endProgram(bool win, string end = "") {
	if (win) printFootStep(end);
	else cout << "-1\n";
	exit(0);
}
void bfs() {
	queue<vector<vector<char>>> q;
	q.push(input);
	setVisited(input);
	while (!q.empty()) {
		int sz = q.size();
		FORJ(sz) {
			vector<vector<char>> front = q.front();
			q.pop();
			FORI(4) {
				bool GAMEOVER = false;
				vector<vector<char>> moved = moveWithDir(front, i, GAMEOVER);
				if (moved == pruning) continue;
				else if (GAMEOVER) {
					makeFootStep(toString(moved), toString(front), i);
					endProgram(true, toString(moved));
				}
				else if (checkVisited(moved)) continue;
				else {
					setVisited(moved);
					q.push(moved);
					makeFootStep(toString(moved), toString(front), i);
				}
			}
		}
		moveCnt++;
		if (moveCnt == 10) endProgram(false);
	}
	// no way out in 10 moves
	endProgram(false);
}

int main(void) {
	fastio;

	vector<char> prune = { 'P' }, over = { 'X' };
	pruning.push_back(prune);
	gameOver.push_back(over);

	cin >> n >> m;
	input.resize(n);
	FORI(n) input[i].resize(m);
	FORI(n) FORJ(m) cin >> input[i][j];
	inputString = toString(input);
	bfs();
}