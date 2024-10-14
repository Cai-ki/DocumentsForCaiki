`Bitwise Balancing <https://codeforces.com/contest/2020/problem/C>`_
===========================================================================

    对于任何二进制位都模拟即可。

    分类讨论。

    .. code-block:: CPP
    
        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <algorithm>
        #include <cmath>
        #include <iostream>

        auto _ = []() {
            freopen("../io/in.txt", "r", stdin);
            freopen("../io/out.txt", "w", stdout);
            return true;
        }();

        #endif

        #define int long long

        void solve() {
            int b, c, d;
            std::cin >> b >> c >> d;

            int a = 0, pos = 0;
            while (b || c || d) {
                int _b = b & 1, _c = c & 1, _d = d & 1;
                int bit;
                if (_d) {
                    if (_b) {
                        bit = 0;
                    } else {
                        if (_c) {
                            a = -1;
                            break;
                        } else {
                            bit = 1;
                        }
                    }
                } else {
                    if (_b) {
                        if (_c) {
                            bit = 1;
                        } else {
                            a = -1;
                            break;
                        }
                    } else {
                        bit = 0;
                    }
                }
                a += (bit << pos);
                b >>= 1, c >>= 1, d >>= 1;
                pos++;
            }

            std::cout << a << '\n';
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
