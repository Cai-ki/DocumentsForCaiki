`Diverse Game <https://codeforces.com/contest/1994/problem/A>`_
=================================================================

    当元素个数为 ``1`` 时特判一下，其它情况只需要将元素 ``+1`` ，而当元素为 ``n * m`` 时赋值为 ``1`` 。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, m;
            std::cin >> n >> m;

            int N = n * m;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < m; j++) {
                    int x;
                    std::cin >> x;

                    if (N == 1) {
                        x = -1;
                    } else if (x == N) {
                        x = 1;
                    } else {
                        x++;
                    }
                    std::cout << x << " \n"[j == m - 1];
                }
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