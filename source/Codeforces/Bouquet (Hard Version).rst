`Bouquet (Hard Version) <https://codeforces.com/contest/1995/problem/B2>`_
==============================================================================

    该题可转化为：分别给出 ``a`` 和 ``a + 1`` 的个数，请求出用这些数字的和凑出的最大的小于 ``m`` 的值。

    我们可以先放 ``a`` ，之后再放 ``a + 1`` 。

    显然如果和小于 ``m`` 意味着我们还有多余的硬币。

    可以发现，如果此时还有 ``a + 1`` 可以通过将原本放进去的 ``a`` 置换出来，就能多花 ``1`` 硬币。

    注意，能提供置换多花的硬币是由剩余的硬币， ``a + 1`` 的剩余数量，放入的 ``a`` 的数量所制约的。 

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            i64 n, m;
            std::cin >> n >> m;
            std::vector<i64> a(n), c(n);
            std::map<i64, i64> cnt;

            for (auto &it : a) {
                std::cin >> it;
            }
            for (int i = 0; i < n; i++) {
                std::cin >> c[i];
                cnt[a[i]] += c[i];
            }

            i64 ans = 0;

            for (auto it : a) {
                i64 _m = m;
                i64 c1 = std::min(_m / it, cnt[it]);
                i64 mid = c1 * it;
                _m -= c1 * it;

                i64 c2 = std::min(_m / (it + 1), cnt[it + 1]);
                mid += c2 * (it + 1);
                _m -= c2 * (it + 1);

                mid += std::min({cnt[it + 1] - c2, _m, c1});

                ans = std::max(ans, mid);
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