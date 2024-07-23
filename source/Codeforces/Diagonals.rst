`Diagonals <https://codeforces.com/contest/1995/problem/A>`_
===================================================================

    暴力即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, k;
            std::cin >> n >> k;

            std::vector<int> cnt(n + 1, 2);
            int ans = 0, x = n;
            cnt[n] = 1;

            while (k > 0) {
                k -= x;
                if (--cnt[x] == 0) {
                    x--;
                }
                ans++;
            }

            std::cout << ans << '\n';
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