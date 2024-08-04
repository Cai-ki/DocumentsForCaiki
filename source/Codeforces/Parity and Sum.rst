`Parity and Sum <https://codeforces.com/contest/1993/problem/B>`_
==========================================================================

    显然一旦数组中存在奇数，操作后的数组必定全奇。

    假如现如今有一个包含奇数和偶数的数组。

    最好的结果是其最大值为奇数，这样只需要进行偶数个数次操作即可结束。

    否则的话，进行偶数个数加 ``1`` 次操作也能保证必定结束（先与最大偶数进行操作，就能得到一个大于所有偶数的奇数）。

    当然这种情况下答案还有可能更优。

    我们只需要让最大的奇数从小到大对偶数操作即可，一旦偶数比它小就可以得到一个更大奇数用来与之后的偶数进行操作。

    而一旦遇到偶数大于我们目前手中的最大奇数，意味着答案就是偶数个数加 ``1``。

    因此我们可以总结得到，排除数组全偶的特殊情况下，其它所有情况的答案只会是偶数个数或者偶数个数加 ``1``

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            i64 max = 0;
            for (auto &it : a) {
                std::cin >> it;
                if (it & 1) {
                    max = std::max(max, (i64)it);
                }
            }

            if (!max) {
                std::cout << "0\n";
                return;
            }

            std::sort(a.begin(), a.end());

            int ans = 0;

            for (int i = 0; i < n; i++) {
                if (a[i] % 2 == 0) {
                    if (max > a[i]) {
                        max += a[i];
                    } else {
                        max += 1e9;
                        ans++;
                    }
                    ans++;
                }
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
