`Min-Fund Prison (Easy) <https://codeforces.com/contest/1970/problem/G1>`_
================================================================================
    
    根据题目范围可以发现，这是一棵树，分析可得加边操作无意义。
    
    对于任意一条边都可以把它作为桥将节点分为两个集合。
    
    我们只需要dfs一遍动态更新最小值即可，注意数据范围，INF要足够大。

    .. code-block:: cpp

        #include <bits/stdc++.h>
        #define all(a) a.begin(), a.end()
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
                solve();
            return 0;
        }