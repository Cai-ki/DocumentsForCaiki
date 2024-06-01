Disks
======

`题目链接 <https://codeforces.com/contest/1949/problem/I>`_

.. code-block:: text
    :caption: 思路

    1. 相切的圆盘会相互影响，我们为它们连一条双向边。
    2. 对于建好的图来说每一个连通块都代表着存在影响链(可能为多条，影响链是一个单链条，无分岔)。
    3. 当两圆盘相切时，无论如何更改，半径和恒定。
    4. 更改任意一个圆盘半径都会造成与该圆盘直接相切的所有圆盘半径反向改变。
    5. 对于任何一条影响链，任意一个非端点节点的变化量的绝对值等于与其相切端点的绝对值。
    6. 如果出现多个影响链相交，我们可以通过染色进行分类，节点一定和其周围节点变化相反，那么一定和其周围节点的周围与其非相切节点变化相同，并且变化量的绝对值都一样。
    7. 我们希望两种类型的节点数量不同，这样我们可以通过让包含更多节点的类型的节点半径减小，从而得到让所有节点半径和减小，
    8. 染色时如果发现矛盾(某节点周围颜色与其相同)则该连通块无法更改任何节点。
    9. 统计两种类型的节点的数量差，如果为0则不存在更改方法。否则存在。

.. code-block:: cpp
    :caption: 代码

    #include <bits/stdc++.h>
    #define all(a) a.begin(), a.end()
    #define int long long
    #define PII pair<int, int>
    using ll = long long;
    const int N = 1e5 + 10, INF = 0x3f3f3f3fll, MOD = 998244353ll;
    using namespace std;

    int get_dis2(PII a, PII b)
    {
        auto [x1, y1] = a;
        auto [x2, y2] = b;
        return abs(x1 - x2) * abs(x1 - x2) + abs(y1 - y2) * abs(y1 - y2);
    }

    void solve()
    {
        int n;
        cin >> n;
        vector<pair<PII, int>> points;
        for (int i = 0; i < n; i++)
        {
            int x, y, r;
            cin >> x >> y >> r;
            points.push_back({{x, y}, r});
        }

        vector<vector<int>> eg(n);

        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
            {
                auto [p1, r1] = points[i];
                auto [p2, r2] = points[j];

                if ((r1 + r2) * (r1 + r2) == get_dis2(p1, p2))
                {
                    eg[i].push_back(j);
                    eg[j].push_back(i);
                }
            }

        vector<int> col(n, -1);

        for (int i = 0; i < n; i++)
        {

            if (col[i] != -1)
                continue;
            col[i] = 0;
            int ok = 1, sum = 0;
            vector<int> q;
            q.push_back(i);

            while (q.size())
            {
                auto u = q.back();
                q.pop_back();

                sum += (col[u] == 0 ? 1 : -1);

                for (auto it : eg[u])
                {
                    if (col[it] == col[u])
                        ok = 0;
                    else if (col[it] == -1)
                    {
                        col[it] = col[u] ^ 1;
                        q.push_back(it);
                    }
                }
            }

            if (ok && sum != 0)
            {
                cout << "YES\n";
                return;
            }
        }

        cout << "NO\n";
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