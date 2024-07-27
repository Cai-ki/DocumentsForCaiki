`Fun <https://codeforces.com/contest/1996/problem/D>`_
===========================================================

    枚举 ``a * b`` ，此时可以发现 ``c`` 的上界可以 ``O(1)`` 算出来。

    那么这种情况的贡献也可以直接算出来加在答案里。

    时间复杂度为 :math:`n*\sqrt n`。

    但这种情况下复杂度依然很危险。

    因此可以在枚举约数时倒着枚举，因为乘积相同时两项越接近它们的和越小，一旦 ``c`` 不存在意味着之后 ``c`` 都不存在，就可以直接 ``break`` 。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n, x;
            std::cin >> n >> x;

            i64 ans = 0;

            for (int i = 1; i < n - 1; i++) {
                for (int j = sqrt(i); j >= 1; j--) {
                    if (i % j != 0) continue;

                    int a = (n - i) / (j + i / j), b = x - j - i / j;
                    a = std::min(a, b);
                    if (a < 1) break;
                    if (j != i / j)
                        ans += 2LL * a;
                    else {
                        ans += a;
                    }
                }
            }

            std::cout << ans << '\n';
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