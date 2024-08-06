`Med-imize <https://codeforces.com/contest/1993/problem/D>`_
===============================================================

    显然题目满足二分性质。

    而如何在 ``check`` 中判断某个值是否满足呢？

    很自然地想到，可以用动态规划来解决。

    在这之前我们需要对给定的数组进行预处理，将原数组大于当前二分的答案的值设置为 ``1`` ， 否则为 ``-1`` 。

    我们会用动态规划来算出题目操作后留下来位置值的和最大是多少。

    当这个值大于 ``0`` 则该答案合法。

    但如何进行动态规划呢？

    这里我们需要了解一些有关题目操作的性质。

    对数组各个位置的下标（从 ``0`` 开始）对 ``k`` 取模，肯定能得到类似于：

    ``0 1 2 ... k - 1 0 1 2 ... k - 1 0 1 2 ... k - 1``

    这里我们用 ``0 1 2 3 0 1 2 3 0 1 2 3`` 代替表示，此时 ``k`` 等于 ``4`` 。

    可以发现一个性质，任意位置对该数组进行操作，假如： ``0 1 2 [3 0 1 2] 3 0 1 2 3`` ，得到的新数组为： ``0 1 2 3 0 1 2 3`` 。

    那么最后得到的数组肯定为： ``0 1 2 3`` （这里原数组长度是 ``k`` 的倍数，若不是则最大值为末尾下标对 ``k`` 取模）。

    说到这，其实思路就很明确了。

    我们其实求的是按顺序选择处理过的下标为 ``0 1 2 3`` 的值的和的最大值。

    ::

        0 1 2 3

        0 1 2 3

        0 1 2 3

    显然二维 ``DP`` 求解一下就可以了，这里不多讲解。

    接下来解释怎么压缩到一维（复杂度和上面都一样但这个会简洁些）。

    设 :math:`dp_i` 为选到以 :math:`i \mod k` 为结尾时的最大值。

    :math:`dp_i = \begin{cases} a_i ,i \mod k = 0 \\  dp_{i-1} +a_i ,otherwise\end{cases},a_i = (a[i] >= u) ? 1 : -1`

    :math:`dp_i = \max(dp_i, dp_{i-k} ),i \ge k`

    完事。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, k;
            std::cin >> n >> k;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            auto check = [&](int u) -> bool {
                std::vector<int> dp(n);
                for (int i = 0; i < n; i++) {
                    int x = (a[i] >= u) ? 1 : -1;
                    if (i % k == 0) {
                        dp[i] = x;
                    } else {
                        dp[i] = dp[i - 1] + x;
                    }
                    if (i >= k) {
                        dp[i] = std::max(dp[i], dp[i - k]);
                    }
                }

                return dp[n - 1] > 0;
            };

            int l = 1, r = 1e9;

            while (l < r) {
                int mid = (l + r + 1) / 2;
                if (check(mid)) {
                    l = mid;
                } else {
                    r = mid - 1;
                }
            }

            std::cout << l << '\n';
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