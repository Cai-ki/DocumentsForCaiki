`Maximum Glutton  <https://atcoder.jp/contests/abc364/tasks/abc364_e>`_
====================================================================================

    显然可以用动态规划。

    最暴力的动态规划是 :math:`O(nxy)` 的时间复杂度，这个比较好想，但肯定会 ``TLE`` 。

    考虑优化。

    暴力的动态规划是直接求答案，我们能不能换个方向想？

    能不能求吃某个数量菜后达到某个 ``x`` 值时 ``y`` 的最小值？显然是可以的。

    最后求答案只需要遍历 ``dp`` 数组找合法情况即可。
    
    这样的时间复杂度为 :math:`O(n^{2}x)` 。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            int n, x, y;
            std::cin >> n >> x >> y;

            std::vector<std::vector<int>> dp(n + 1, std::vector<int>(x + 1, y + 1));

            dp[0][0] = 0;

            for (int i = 0; i < n; i++) {
                int a, b;
                std::cin >> a >> b;

                for (int j = i; j >= 0; j--) {
                    for (int k = x; k >= a; k--) {
                        dp[j + 1][k] = std::min(dp[j + 1][k], dp[j][k - a] + b);
                    }
                }
            }

            int ans = 0;

            for (int i = 0; i < n; i++) {
                for (int j = 0; j <= x; j++) {
                    if (dp[i][j] <= y) {
                        ans = std::max(ans, i + 1);
                    }
                }
            }

            std::cout << ans << '\n';

            return 0;
        }