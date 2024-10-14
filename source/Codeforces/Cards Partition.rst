`Cards Partition <https://codeforces.com/contest/2019/problem/C>`_
=======================================================================

    ``n`` 的范围不大。可以考虑暴力从大到小枚举答案，判断是否合法。

    对于原本自带的牌，其实我们只在意数量最多的那种牌即可，也就是说，牌堆的数量保底得有 ``max`` 个。

    由于我们枚举的是牌堆的大小，显然大小必须小于等于所有牌的数量。

    之后就简单了，我们可以算出牌堆数量的上下界，牌堆的大小固定，牌数最多是加上超市和自己最初拥有的牌，最少是只有自己最初拥有的牌。

    我们只需要寻找是否有某个牌堆数量恰好整除牌数即可。

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
            int n, k;
            std::cin >> n >> k;
            std::vector<int> a(n);

            int sum = 0;

            for (auto &it : a) {
                std::cin >> it;
                sum += it;
            }

            int max = *std::max_element(a.begin(), a.end());

            for (int i = n; i >= 1; i--) {
                if (i * max > sum + k) {
                    continue;
                }

                int x = (sum + k) / i, y = sum / i;
                bool ok = ((sum + k) % i == 0) | (sum % i == 0);
                if (x == y && !ok) {
                    continue;
                }

                std::cout << i << '\n';
                return;
            }
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