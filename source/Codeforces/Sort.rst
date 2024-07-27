`Sort <https://codeforces.com/contest/1996/problem/C>`_
=============================================================

    用前缀和维护区间信息。

    对于任意一次询问，我们获得的区间值是这个区间下两个字符串中某个字母数量的差。

    如果为 ``0`` 意味着不需要额外的操作就能让这个字母合法，也就意味着这个字母所在的位置两两对应。

    不为 ``0`` 则意味着这些位置需要进行匹配。

    把所有需要操作的位置统计出来，并除以 ``2`` 。

    这个值实际上应该是两个字符串不匹配位置的数量和，当然你可以只统计正数和或者负数和，很显然它们的绝对值是相等的。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, q;
            std::cin >> n >> q;

            std::string a, b;
            std::vector c(n + 1, std::vector<int>(26, 0));

            std::cin >> a >> b;

            for (int i = 0; i < n; i++) {
                c[i + 1][a[i] - 'a'] += 1;
                c[i + 1][b[i] - 'a'] -= 1;
                for (int j = 0; j < 26; j++) {
                    c[i + 1][j] += c[i][j];
                }
            }

            while (q--) {
                int l, r;
                std::cin >> l >> r;

                int ans = 0;
                for (int i = 0; i < 26; i++) {
                    ans += abs(c[r][i] - c[l - 1][i]);
                }
                std::cout << ans / 2 << '\n';
            }
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