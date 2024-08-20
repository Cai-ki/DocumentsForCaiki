`Generate Permutation <https://codeforces.com/contest/2001/problem/B>`_
=================================================================================

    按奇偶性分类讨论就行。

    显然奇数情况可以从中间往后递增填数字，之后前面的位置就由剩下的数字倒序插入即可。

    偶数情况则不可能构造出数列。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        void solve() {
            int n;
            std::cin >> n;

            if (n & 1) {
                int cnt = n / 2;
                int p = n;
                std::vector<int> a;
                while (cnt--) {
                    a.push_back(p);
                    p--;
                }
                a.push_back(1);
                p = 2;
                cnt = n / 2;
                while (cnt--) {
                    a.push_back(p);
                    p++;
                }

                for (auto it : a) {
                    std::cout << it << ' ';
                }
                std::cout << '\n';
            } else {
                std::cout << -1 << '\n';
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