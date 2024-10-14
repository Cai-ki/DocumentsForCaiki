`Index and Maximum Value <https://codeforces.com/contest/2007/problem/B>`
===============================================================================

    由于是对于具有给定大小的值的位置进行操作，我们可以发现，最初的最大值的位置，也是最后最大值的位置。

    找到最初的位置，模拟操作就行。

    .. code-block::CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <algorithm>
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
            int n, m;
            std::cin >> n >> m;
            std::vector<int> a(n);
            for (auto &it : a) {
                std::cin >> it;
            }

            int max = *std::max_element(a.begin(), a.end());

            while (m--) {
                char c;
                int l, r;
                std::cin >> c >> l >> r;
                if (max >= l && max <= r) {
                    max += (c == '+') ? 1LL : -1LL;
                }
                std::cout << max << ' ';
            }
            std::cout << '\n';
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