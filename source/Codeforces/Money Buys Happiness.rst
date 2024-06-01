Money Buys Happiness
=======================

`题目链接 <https://codeforces.com/contest/1974/problem/E>`_

.. code-block:: text
    :caption: 思路

    1.很显然的一道动态规划题，我们发现x，和cost都蛮大的，但happiness却很小，那么我们定义状态尽量往它靠近。
    2.所以我们定义dp[i]表示，幸福度为i下我们能得到的最多的钱。
    3.以上定义是在我们压一维度下的体现，其实原本应该是两维，即第一维表示天数，第二维表示幸福度，但考虑到多测多次申请太多内存的开销问题，我们进行压维处理。
    4.那么我们状态转移具体操作如下：
        for (int i = 1; i < n; i++)
            for (int k = top, next; (next = k + happiness[i]) && k >= 0; k--)
                if (dp[k] >= 0 && (dp[k] += x) >= cost[i])
                    dp[next] = max(dp[next], dp[k] - cost[i]), ans = max(ans, next);
    5.由于经过作者压行处理，可读性不是很好，我们可以类比背包问题进行理解，这里作者不进行详细解释。
    6.特别注意的是我们每一天最多买一次，那么我们需要类别01背包的模型。还有当天赚的钱当天并不能使用，这就是我进行dp[k] += x的原因，即拿到前一天的工资。


.. code-block:: cpp
    :caption: 代码

    #include <bits/stdc++.h>
    #define all(a) a.begin(), a.end()
    #define int long long
    #define PII pair<int, int>
    using ll = long long;
    const int N = 1e5 + 10, INF = 1e18, MOD = 1e9 + 7;
    using namespace std;

    void solve()
    {
        int n, x;
        cin >> n >> x;
        vector<int> cost(n), happiness(n);
        for (int i = 0; i < n; i++)
            cin >> cost[i] >> happiness[i];

        int top = accumulate(all(happiness), 0ll), ans = 0;
        vector<int> dp(2e5, -1);
        dp[0] = 0;

        if (!cost[0])
            dp[ans = happiness[0]] = 0;

        for (int i = 1; i < n; i++)
            for (int k = top, next; (next = k + happiness[i]) && k >= 0; k--)
                if (dp[k] >= 0 && (dp[k] += x) >= cost[i])
                    dp[next] = max(dp[next], dp[k] - cost[i]), ans = max(ans, next);

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