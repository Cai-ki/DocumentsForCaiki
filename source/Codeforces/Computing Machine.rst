`Computing Machine <https://codeforces.com/problemset/problem/1978/E>`_
===============================================================================

    名不副实的 ``2000`` 分题。

    发现 ``l + 2`` 到 ``r - 2`` 可以用前缀和数组维护，剩下的部分暴力特判即可。

    唯一的难点在于特判的条件需要仔细斟酌。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::string s, t;
            std::cin >> s >> t;

            std::vector<int> pre(n + 1, 0);

            for (int i = 0; i < n; i++) {
                pre[i + 1] =
                    pre[i] + (i >= 2 && i + 2 < n &&
                            (s[i] == '1' || ((t[i - 1] == '1' || s[i - 2] == '0') &&
                                            (t[i + 1] == '1' || s[i + 2] == '0'))));
            }

            int q;
            std::cin >> q;

            while (q--) {
                int l, r;
                std::cin >> l >> r;
                l--;

                int ans = 0;
                if (r - l >= 5) {
                    ans += pre[r - 2] - pre[l + 2];
                }

                for (int i = l; i < r; i++) {
                    if (i >= l + 2 && i < r - 2) {
                        i = r - 2;
                    }

                    ans += (s[i] == '1' || (((i - 1 >= l && t[i - 1] == '1') ||
                                            (i - 2 >= l && s[i - 2] == '0')) &&
                                            ((i + 1 < r && t[i + 1] == '1') ||
                                            (i + 2 < r && s[i + 2] == '0'))));
                }

                std::cout << ans << '\n';
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