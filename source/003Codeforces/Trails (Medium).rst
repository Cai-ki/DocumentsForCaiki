Trails (Medium)
==================

`题目链接 <https://codeforces.com/problemset/problem/1970/E2>`_

.. code-block:: text
    :caption: 思路

    1.首先我们继承Trails (Easy)的整体思路。
    2.我们发现n的范围很大，同时观察Trails (Easy)的更新状态过程。
    3.可以考虑用矩阵快速幂，对于每一天的(矩阵)dp，我们都可以由(矩阵)cnt*dp(前一天)得到，化简得dp = ((cnt)^n)*dp。
    4.这里的dp是一个二维数组，和Trails (Easy)中的不同。第一维大小为m，第二维大小为1。这是为了方便矩阵乘法。
    5.最后求和即可。

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
    const int N = 1e5 + 10, INF = 0x3f3f3f3fll, MOD = 1e9 + 7;
    using namespace std;

    vector<vector<int>> multiply(vector<vector<int>> a, vector<vector<int>> b)
    {
        int n = a.size(), k = a[0].size(), m = b[0].size();
        vector<vector<int>> res(n, vector<int>(m));
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                for (int l = 0; l < k; l++)
                    res[i][j] = (res[i][j] + a[i][l] * b[l][j]) % MOD;
        return res;
    }

    vector<vector<int>> qpow(vector<vector<int>> a, int n)
    {
        vector<vector<int>> tmp(a.size(), vector<int>(a[0].size(), 0));

        for (int i = 0; i < a.size(); i++)
            tmp[i][i] = 1;

        while (n)
        {
            if (n & 1)
                tmp = multiply(tmp, a);
            a = multiply(a, a);
            n >>= 1;
        }

        return tmp;
    }
    void solve()
    {
        int m, n;
        cin >> m >> n;

        vector<int> s(m), l(m);

        for (int i = 0; i < m; i++)
            cin >> s[i];
        for (int i = 0; i < m; i++)
            cin >> l[i];

        vector<vector<int>> dp(m, {0}), cnt(m, vector<int>(m));
        dp[0][0] = 1;

        for (int i = 0; i < m; i++)
            for (int j = 0; j < m; j++)
                cnt[i][j] = cnt[j][i] = (s[i] * s[j] + s[i] * l[j] + l[i] * s[j]) % MOD;

        dp = multiply(qpow(cnt, n), dp);

        int ans = 0;

        for (int i = 0; i < m; i++)
            ans = (ans + dp[i][0]) % MOD;

        cout << ans << '\n';
    }
    signed main()
    {
        ios::sync_with_stdio(0), cin.tie(0);
        int T = 1;
        // cin >> T, cin.get();
        while (T--)
        {
            solve();
        }
        return 0;
    }