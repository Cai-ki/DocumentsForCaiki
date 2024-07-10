Integral Array
=======================

    `题目链接 <https://codeforces.com/problemset/problem/1648/B>`_

    .. code-block:: text
        :caption: 思路

        1.这道题的暴力做法显然是n^2。
        2.我们发现c的范围很小，想办法往c上考虑。
        3.对于题目中的x >= y，枚举x显然是暴力做法，我们尝试枚举y。
        4.假设(x / y)向下取整得到z，我们发现(y * z) <= x <= c。
        5.所以可以开两层for循环分别枚举y和z，一旦(y * z) > x，就break。
        6.由于y是除数，并且向下取整，我们可以根据y，z的值求出x的范围(y * z, y * z + y - 1)
        7.我们可以通过前缀和O(1)求出某个范围是否有数字存在于数组。
        8.如果数组中存在满足(y * z, y * z + y - 1)的数字，那么必须也要存在相应的z。
        9.存在满足(y * z, y * z + y - 1)的数字，但并不存在相应的z，我们将输出No并return。
        10.最后输出Yes即可。
        11.在双层循环都是枚举y，z的数值的情况下，时间复杂度为O(C*log(C))。

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
            int n, c;
            cin >> n >> c;

            vector<int> cnt(c + 1, 0);

            for (int i = 0; i < n; i++)
            {
                int x;
                cin >> x;
                cnt[x]++;
            }

            for (int i = 1; i <= c; i++)
                cnt[i] += cnt[i - 1];

            for (int i = 1; i <= c; i++)
            {
                if (cnt[i] == cnt[i - 1])
                    continue;

                for (int j = 1; i * j <= c; j++)
                {
                    int l = i * j, r = min(c, l + i - 1);

                    if (cnt[r] - cnt[l - 1])
                        if (cnt[j] == cnt[j - 1])
                        {
                            cout << "No\n";
                            return;
                        }
                }
            }

            cout << "Yes\n";
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