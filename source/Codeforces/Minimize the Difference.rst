`Minimize the Difference <https://codeforces.com/contest/2013/problem/D>`_
================================================================================

    二分 ``+`` 贪心。

    二分最大的最小值，之后再二分最小的最大值。

    前者比较简单，而后者需要贪心的构造，具体思路看下方代码。

    .. code-block:: CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <iostream>
        #include <vector>

        auto _ = []() {
            freopen("../io/in.txt", "r", stdin);
            freopen("../io/out.txt", "w", stdout);
            return true;
        }();

        #endif

        #define int long long

        void solve() {
            int n;
            std::cin >> n;

            std::vector<int> a(n);
            for (auto &it : a) {
                std::cin >> it;
            }

            auto check1 = [&](int u) -> bool {
                int x = 0;
                for (auto it : a) {
                    if (it > u) {
                        x += it - u;
                    } else {
                        x -= u - it;
                        if (x < 0) {
                            return false;
                        }
                    }
                }

                return true;
            };

            int l = 0, r = 1e18;
            while (l < r) {
                int mid = (l + r + 1) >> 1;
                if (check1(mid)) {
                    l = mid;
                } else {
                    r = mid - 1;
                }
            }

            int min = l;

            auto check2 = [&](int u) -> bool {
                int x = 0;
                std::vector<int> b(n + 1, 0), more(n + 1);

                for (int i = n - 1; i >= 0; i--) {
                    b[i] = b[i + 1];
                    if (a[i] < min) {
                        b[i] += min - a[i];
                    } else {
                        more[i] = a[i] - min - std::min(b[i], a[i] - min);
                        b[i] -= std::min(b[i], a[i] - min);
                    }
                }

                for (int i = 0; i < n; i++) {
                    x += more[i];
                    x -= std::min(u - min, x);
                }

                return x == 0;
            };

            r = 1e18;
            while (l < r) {
                int mid = (l + r) >> 1;
                if (check2(mid)) {
                    r = mid;
                } else {
                    l = mid + 1;
                }
            }

            std::cout << l - min << '\n';
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