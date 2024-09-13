`Satyam and Counting <https://codeforces.com/contest/2009/problem/D>`_
==========================================================================

    暴力加分类讨论就行。

    ::

        for (auto it : {0, 1}) {
        
        }

    对于枚举纵坐标时，我觉得这个写法比较优雅。

    .. code-block:: CPP

        #include <iostream>
        #include <vector>

        #define int long long

        void solve() {
            int n;
            std::cin >> n;

            std::vector<std::vector<int>> a(n + 3, std::vector<int>(2, 0));

            for (int i = 0; i < n; i++) {
                int x, y;
                std::cin >> x >> y;
                x++;
                a[x][y] = 1;
            }

            auto c = a;

            for (int i = 1; i <= n + 1; i++) {
                c[i][0] += c[i - 1][0];
                c[i][1] += c[i - 1][1];
            }

            int cnt = 0;
            for (int i = 1; i <= n + 1; i++) {
                for (auto it : {0, 1}) {
                    if (a[i][it]) {
                        if (a[i - 1][it ^ 1] && a[i + 1][it ^ 1]) {
                            cnt++;
                        }
                        if (a[i][it ^ 1]) {
                            cnt += c[n + 1][it ^ 1] - c[i][it ^ 1];
                            cnt += c[i - 1][it ^ 1] - c[0][it ^ 1];
                        }
                    }
                }
            }

            std::cout << cnt << '\n';
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