`Splitting Items <https://codeforces.com/contest/2004/problem/C>`_
===========================================================================

    很显然 ``B`` 最好的成绩也就是差值为 ``0`` 罢了。

    对原数组降序排列。

    ``A`` 和 ``B`` 的选择显然固定。

    只需要让 ``B`` 的选择尽可能靠近是一个 ``A`` 的选择就行。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        void solve() {
            int n, k;
            std::cin >> n >> k;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            std::sort(a.begin(), a.end(), std::greater<int>());

            for (int i = 0; i < n / 2; i++) {
                if (k > 0) {
                    int add = std::min(k, a[i * 2] - a[i * 2 + 1]);
                    a[i * 2 + 1] += add;
                    k -= add;
                }
            }

            int A = 0, B = 0;

            for (int i = 0; i < n; i++) {
                if (i & 1) {
                    B += a[i];
                } else {
                    A += a[i];
                }
            }

            std::cout << A - B << '\n';
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
