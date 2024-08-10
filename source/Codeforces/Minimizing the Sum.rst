`Minimizing the Sum <https://codeforces.com/problemset/problem/1969/C>`_
==========================================================================

    发现 ``k`` 很小，可以考虑动态规划，因为你可以假设某个下标为操作区间的左端点，然后枚举操作数即可，这样这个操作区间的贡献就是区间最小值乘上区间大小，把答案转移到区间右界下一位置即可。

    :math:`dp_{i,x}` 表示走到下标 ``i`` 还未算 ``i`` 和其往后的贡献并且剩下 ``x`` 个操作数时的最小和。

    可以考虑递推来更新状态。

    枚举 ``j`` 代表再进行多少步操作。

    :math:`dp_{i + j + 1,x + j} = \max dp_{i,x} + min * (j + 1)` 

    这里的 ``min`` 代表区间 ``i`` 到 ``i + j`` 的最小值。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        constexpr i64 INF = 1E18;

        void solve() {
            int n, k;
            std::cin >> n >> k;
            std::vector<int> a(n);
            for (auto &it : a) {
                std::cin >> it;
            }

            std::vector<std::vector<i64>> dp(n + 1, std::vector<i64>(k + 1, INF));

            dp[0][0] = 0;
            for (int i = 0; i < n; i++) {
                int min = a[i];
                for (int j = 0; j <= k && i + j < n; j++) {
                    min = std::min(min, a[i + j]);
                    for (int x = 0; x + j <= k; x++) {
                        dp[i + j + 1][x + j] = std::min(dp[i + j + 1][x + j],
                                                        dp[i][x] + 1LL * min * (j + 1));
                    }
                }
            }

            std::cout << *std::min_element(dp[n].begin(), dp[n].end()) << '\n';
        }

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int t;
            std::cin >> t;

            while (t--) {
                solve();
            }

            return 0;
        }