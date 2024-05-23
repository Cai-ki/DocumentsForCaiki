Min-Fund Prison (Easy)
=======================

`题目链接 <https://codeforces.com/contest/1970/problem/G1>`_

.. code-block:: text
    :caption: 思路

    1.根据题目范围可以发现，这是一棵树，分析可得加边操作无意义。
    2.对于任意一条边都可以把它作为桥将节点分为两个集合。
    3.我们只需要dfs一遍动态更新最小值即可，注意数据范围，INF要足够大。

.. code-block:: cpp
    :caption: 代码

    #include <bits/stdc++.h>
    // #pragma GCC optimize (1)
    // #pragma GCC optimize (2)
    // #pragma GCC optimize (3)
    #define all(a) a.begin(), a.end()
    #define ff first
    #define ss second
    #define int long long
    #define PII pair<int, int>
    using ll = long long;
    const int N = 1e5 + 10, INF = 1e18, MOD = 1e9 + 7;
    using namespace std;

    void solve()
    {
        int n, m, k;
        cin >> n >> m >> k;

        vector<vector<int>> eg(n);

        while (m--)
        {
            int u, v;
            cin >> u >> v;
            u--, v--;
            eg[u].push_back(v);
            eg[v].push_back(u);
        }

        int ans = INF;

        auto dfs = [&](auto &&dfs, int u, int fa) -> int
        {
            int res = 1;
            for (auto v : eg[u])
            {
                if (v == fa)
                    continue;
                res += dfs(dfs, v, u);
            }

            ans = min(ans, res * res + (n - res) * (n - res));

            return res;
        };

        dfs(dfs, 0, -1);

        cout << ans << '\n';
    }
    signed main()
    {
        ios::sync_with_stdio(0), cin.tie(0);
        int T = 1;
        cin >> T, cin.get();
        while (T--)
        {
            solve();
        }
        return 0;
    }