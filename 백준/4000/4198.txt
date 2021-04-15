#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
#include <string>
#include <map>
#include <queue>
#include <stack>
#include <set>
#define fastio ios::sync_with_stdio(0), cin.tie(0), cout.tie(0)
#define lint long long
#define FORI(n) for(int i = 0; i < n; i++)
#define FORJ(n) for(int j = 0; j < n; j++)
#define FORK(n) for(int k = 0; k < n; k++)
int dir[4][2] = { {-1, 0}, {1, 0}, {0, 1}, {0, -1} };

// 2021 03 24

using namespace std;

int n, answer = 0;
int v[2001];
int LIS[2001], LDS[2001];
// LIS, LDS : [INDEX, 끝] 범위에서의 LIS, LDS 최대 길이를 의미
// 구하는 법 : 맨 오른쪽에서부터 왼쪽으로 각각 LDS, LIS의 최대 길이를 구하면 된다.(반대로 생각)

int main(void) {
	fastio;

	cin >> n;
	for (int i = 1; i <= n; i++) cin >> v[i];
	LIS[n] = 1; LDS[n] = 1;
	for (int i = n - 1; i >= 0; i--) {
		LIS[i] = 1; LDS[i] = 1;
		for (int j = n; j > i; j--) {
			if (v[i] < v[j]) LIS[i] = max(LIS[i], LIS[j] + 1);
			if (v[i] > v[j]) LDS[i] = max(LDS[i], LDS[j] + 1);
		}
	}
	int answer = 0;
	for (int i = 1; i <= n; i++) answer = max(answer, LIS[i] + LDS[i] - 1);
	cout << answer << '\n';
}