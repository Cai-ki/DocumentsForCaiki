`Nikita and LCM <https://codeforces.com/problemset/problem/1977/C>`_
==========================================================================

    先求所有元素的 ``LCM`` 特判答案为 ``n`` 的情况。

    若答案必定不为 ``n`` 则意味着答案是所有元素的 ``LCM`` 的约数。

    这个约数肯定是小于 ``1E9`` 的数字。

    枚举约数，求所有为该约数的约数的元素公共的 ``LCM`` ，判断该 ``LCM`` 是否等于约数，是则更新答案即可。

    显然，时间复杂度为 :math:`n \log n \sqrt{\max a}`

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);
            for (auto &it : a) {
                std::cin >> it;
            }

            i64 x = 1;
            for (auto it : a) {
                x = std::lcm(x, 1LL * it);
                if (x > 1E9) {
                    break;
                }
            }

            if (std::find(a.begin(), a.end(), x) == a.end()) {
                std::cout << n << '\n';
                return;
            }

            int ans = 0;

            auto check = [&](int d) {
                if (std::find(a.begin(), a.end(), d) != a.end()) {
                    return;
                }
                int lcm = 1;
                int cnt = 0;
                for (int i = 0; i < n; i++) {
                    if (d % a[i] == 0) {
                        lcm = std::lcm(lcm, a[i]);
                        cnt++;
                    }
                }
                if (lcm == d) {
                    ans = std::max(ans, cnt);
                }
            };

            for (int i = 1; i * i <= x; i++) {
                if (x % i == 0) {
                    check(i), check(x / i);
                }
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