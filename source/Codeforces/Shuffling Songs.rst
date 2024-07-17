`Shuffling Songs <https://codeforces.com/contest/1950/problem/G>`_
====================================================================

    n的范围很小，我们考虑转化为状压dp。
    
    前期的准备工作就是对字符串进行映射。
    
    考虑设计dp状态，dp[mask][lst],mask表示当前我们的点集，lst表示下一个进入的点会加在lst点后，dp[mask][lst]的值表示当前状态是否合法。
    
    初始化时将所有点集只有一个点的状态设为true，显然只有一项时肯定合法。
    
    考虑状态转移，在dp[mask][lst]合法的前提下，我们枚举之后可能会加入的点(保证其不在我们已有的点集内)，进行以下转移。
    
    .. code-block:: cpp    
        
        if (a[i] == a[lst] || b[i] == b[lst])
            dp[mask | (1 << i)][i] |= dp[mask][lst];
    
    遍历所有合法情况统计当前点集数量，得到最大值，最后需要删除的点就是除去点集合外剩下的部分。

    .. code-block:: cpp

        #include <bits/stdc++.h>
        #define all(a) a.begin(), a.end()
        #define int long long
        #define PII pair<int, int>
        using ll = long long;
        const int N = 1e5 + 10, INF = 0x3f3f3f3fll, MOD = 998244353ll;
        using namespace std;

        void solve()
        {
            int n, cnt = 0, ans = 0;
            cin >> n;
            map<string, int> id;
            vector<int> a(n), b(n);
            for (int i = 0; i < n; i++)
            {
                string s1, s2;
                cin >> s1 >> s2;
                if (!id.count(s1))
                {
                    id[s1] = cnt++;
                }
                if (!id.count(s2))
                {
                    id[s2] = cnt++;
                }
                a[i] = id[s1], b[i] = id[s2];
            }

            vector<vector<int>> dp((1 << n), vector<int>(n, 0));

            for (int i = 0; i < n; i++)
                dp[1 << i][i] = 1;

            for (int mask = 0; mask < (1 << n); mask++)
                for (int lst = 0; lst < n; lst++)
                {
                    if (!dp[mask][lst])
                        continue;
                    for (int i = 0; i < n; i++)
                    {
                        if (mask >> i & 1)
                            continue;
                        if (a[i] == a[lst] || b[i] == b[lst])
                            dp[mask | (1 << i)][i] |= dp[mask][lst];
                    }
                }

            for (int mask = 0; mask < (1 << n); mask++)
                for (int lst = 0; lst < n; lst++)
                    if (dp[mask][lst])
                        ans = max(ans, (int)__builtin_popcount(mask));

            cout << n - ans << '\n';
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