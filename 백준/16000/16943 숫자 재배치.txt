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

int a, b, c = -1;
vector<int> v;
vector<bool> visited;
int tempC = 0;

void permu(int count) {
	if (count == v.size())
		if (tempC <= b) c = max(c, tempC);
		else return;
	else
		for (int i = 0; i < v.size(); i++) {
			if (visited[i]) continue;
			if (tempC == 0 && v[i] == 0) continue;
			tempC *= 10;
			tempC += v[i];
			visited[i] = true;
			permu(count + 1);
			visited[i] = false;
			tempC -= v[i];
			tempC /= 10;
		}
}

int main(void) {
	fastio;

	cin >> a >> b;
	while (a) {
		v.push_back(a % 10);
		a /= 10;
	}
	visited.resize(v.size());
	permu(0);
	if (c == -1) cout << -1 << '\n';
	else cout << c << '\n';
}