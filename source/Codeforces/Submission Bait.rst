`Submission Bait <https://codeforces.com/contest/1990/problem/A>`_
===========================================================================

    统计每个数字出现的次数，如果有数字出现的次数为奇数，先手必胜，反之必败。

    必胜策略是先手选择数字最大的并且出现次数为奇数的任意索引操作，之后模仿后手操作即可。

    必输策略是无论先手选择哪个索引进行操作，后手都可以模仿先手选择的操作，后手必胜。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;

            std::vector<int> cnt(n + 1, 0);

            while (n--) {
                int x;
                std::cin >> x;
                cnt[x]++;
            }

            for (int it : cnt) {
                if (it & 1) {
                    std::cout << "YES\n";
                    return;
                }
            }

            std::cout << "NO\n";
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