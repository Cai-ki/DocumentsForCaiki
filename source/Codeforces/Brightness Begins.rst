`Brightness Begins <https://codeforces.com/contest/2020/problem/B>`
===========================================================================

    发现任意位置的灯的亮灭是固定的，不会随着 ``n`` 的改变而改变（假如该位置存在的话）。

    任意位置之后由小于它的约数位置影响，在 ``1`` 到该位置之间，除了完全平方数以外，约数的个数都是偶数，这样就意味着，该位置的状态为亮。

    接下来就很简单了，二分答案即可。

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
            int k;
            std::cin >> k;
            int l = 1, r = 2e18;

            auto check = [&](int u) -> bool { return u - (int)std::sqrt(u) >= k; };

            while (l < r) {
                int mid = (l + r) >> 1;
                if (check(mid)) {
                    r = mid;
                } else {
                    l = mid + 1;
                }
            }

            std::cout << l << '\n';
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