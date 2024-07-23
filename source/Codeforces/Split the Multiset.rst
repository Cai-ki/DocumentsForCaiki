`Split the Multiset <https://codeforces.com/contest/1988/problem/A>`_
==================================================================================

    尽可能让每次操作都分离出更多的 ``1`` ，显然每次操作应将数字拆分出 ``k - 1`` 个 ``1`` 。

    对剩下的部分重复贪心操作即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, k;
            std::cin >> n >> k;

            int res = 0;

            while (n > 1) {
                n = n - k + 1;
                res++;
            }

            std::cout << res << '\n';
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
