`Test of Love <https://codeforces.com/problemset/problem/1992/D>`_
=======================================================================

    设 :math:`dp_i` 表示跳到 :math:`i` 点时需要游的最小里程。

    考虑用 :math:`to` 型来更新其它状态。

    若当前位置为原木，则可以更新所有能跳到的地方，并且不用增加额外的游泳里程。

    若当前位置为水中，则只能更新下一个位置，并使得里程加 :math:`1` 。

    若为鳄鱼显然为非法情况，不用更新其它位置。

    最后若 :math:`dp[n + 1] \le k` 则认为可以到对岸。 

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, m, k;
            std::string s;
            std::cin >> n >> m >> k >> s;
            s = "L" + s + 'L';

            std::vector<int> dp(n + 3, k + 1);
            dp[0] = 0;

            for (int i = 0; i <= n; i++) {
                if (s[i] == 'L') {
                    for (int j = 1; j <= m && i + j <= n + 1; j++) {
                        dp[i + j] = std::min(dp[i + j], dp[i]);
                    }
                } else if (s[i] == 'W') {
                    dp[i + 1] = std::min(dp[i + 1], dp[i] + 1);
                }
            }

            if (dp[n + 1] <= k) {
                std::cout << "YES\n";
            } else {
                std::cout << "NO\n";
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