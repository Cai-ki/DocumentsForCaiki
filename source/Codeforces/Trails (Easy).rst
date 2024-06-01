Trails (Easy)
==================

`题目链接 <https://codeforces.com/problemset/problem/1970/E1>`_

.. code-block:: text
    :caption: 思路

    1.很显然是一道动态规划题，我们考虑dp解决，时间复杂度O(n*m*m)可过。
    2.设计dp状态，dp[i]，这一天以i为终点的方案数。
    3.初始化dp[0] = 1，其他都为0，预处理出任意两个房屋直接路径的条数。
    4.进行状态转移，每一天我们都需要枚举出发点和结束点。
        next[j] = (next[j] + dp[i] * cnt[i][j] % MOD) % MOD;
    5.由于压缩了一维，每一次记得更新dp数组。             
    6.最后求和即可获得全部方案数。

.. code-block:: cpp
    :caption: 代码

    #include <bits/stdc++.h>
    #define all(a) a.begin(), a.end()
    #define int long long
    #define PII pair<int, int>
    using ll = long long;
    const int N = 1e5 + 10, INF = 0x3f3f3f3fll, MOD = 1e9 + 7;
    using namespace std;

    void solve()
    {
        int m, n;
        cin >> m >> n;

        vector<int> s(m), l(m), dp(m, 0);

        for (int i = 0; i < m; i++)
            cin >> s[i];
        for (int i = 0; i < m; i++)
            cin >> l[i];

        vector<vector<int>> cnt(m, vector<int>(m));

        dp[0] = 1;

        for (int i = 0; i < m; i++)
            for (int j = 0; j < m; j++)
                cnt[i][j] = cnt[j][i] = (s[i] * s[j] + s[i] * l[j] + l[i] * s[j]) % MOD;

        for (int x = 0; x < n; x++)
        {
            vector<int> next(m, 0);
            for (int i = 0; i < m; i++)
                for (int j = 0; j < m; j++)
                    next[j] = (next[j] + dp[i] * cnt[i][j] % MOD) % MOD;
            swap(next, dp);
        }

        int ans = 0;

        for (int i = 0; i < m; i++)
            ans = (ans + dp[i]) % MOD;

        cout << ans << '\n';
    }
    signed main()
    {
        ios::sync_with_stdio(0), cin.tie(0);
        int T = 1;
        // cin >> T, cin.get();
        while (T--)
            solve();
        return 0;
    }