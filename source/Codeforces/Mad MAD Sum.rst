`Mad MAD Sum <https://codeforces.com/contest/1990/problem/C>`_
==================================================================

    模拟题目操作后可以发现一个特殊性质，第一次操作后数组呈现递增趋势，第二次操作后数组的相同元素数量至少为 ``2`` （除末尾元素）。

    对于两次操作后的数组也有一个特殊性质，可以发现之后接着模拟题目操作数组的非零项会整体右移，那么之后每一项的贡献就能计算出来。

    任意一项的贡献是其在数组上的值乘上这个元素在逆置数组的第几位。

    .. code-block:: CPP

            #include <bits/stdc++.h>

            using i64 = long long;

            void solve() {
                int n;
                std::cin >> n;

                std::vector<int> a(n);
                for (int i = 0; i < n; i++) {
                    std::cin >> a[i];
                }

                i64 ans = 0;

                for (int t = 0; t < 2; t++) {
                    std::vector<int> vis(n + 1);
                    int mx = 0;
                    for (auto &x : a) {
                        ans += x;
                        if (vis[x]) {
                            mx = std::max(mx, x);
                        }
                        vis[x] = 1;
                        x = mx;
                    }
                }
                for (int i = 0; i < n; i++) {
                    ans += 1LL * (n - i) * a[i];
                }
                std::cout << ans << "\n";
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
