`Color Rows and Columns <https://codeforces.com/contest/2000/problem/F>`_
=================================================================================

    贪心的方法不好想。

    不过题目给定数据范围很小。

    考虑能不能 ``DP`` 。

    发现每一个矩形可能产生的贡献并不多，最多 :math:`a_i+b_i` ，显然可以模拟贪心过程处理出来。

    每次选择较短的边涂颜色，长边长度减一，持续这个操作就行。

    :math:`cnt_{i,k}` 表示第 ``i`` 个矩形，贡献为 ``k`` 时需要的成本。

    处理出来这个之后，就是一个典型的 ``01`` 背包问题了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        void solve() {
            int n, k;
            std::cin >> n >> k;

            std::vector<std::vector<int>> cnt(n, std::vector<int>(201, 1e9));

            for (int i = 0; i < n; i++) {
                int x, y;
                std::cin >> x >> y;
                if (x > y) std::swap(x, y);
                int mid = 0, idx = 1;
                cnt[i][0] = 0;
                while (y > 0) {
                    mid += x;
                    cnt[i][idx++] = mid;
                    y--;
                    if (x > y) std::swap(x, y);
                }
            }

            std::vector<int> dp(k + 1, 1e9);
            dp[0] = 0;

            for (int i = 0; i < n; i++) {
                for (int j = k; j >= 0; j--) {
                    for (int z = 0; z <= j; z++) {
                        dp[j] = std::min(dp[j], cnt[i][z] + dp[j - z]);
                    }
                }
            }

            if (dp[k] < 1e9)
                std::cout << dp[k] << '\n';
            else {
                std::cout << -1 << '\n';
            }
        }

        signed main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int t;
            std::cin >> t;

            while (t--) {
                solve();
            }

            return 0;
        }