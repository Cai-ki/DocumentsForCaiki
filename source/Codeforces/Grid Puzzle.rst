`Grid Puzzle <https://codeforces.com/contest/1990/problem/D>`_
==================================================================

    答案最大不超过 ``n`` 。

    也就是说，对于任意一行，最多进行 ``1`` 次操作，显然当你多次对某一行进行操作，所影响的只有下一行罢了，但多进行的操作完全可以染白一整行甚至更多。

    那么解题思路就很显而易见了。

    使用贪心的思想，从上往下贪，假如这一行需要操作，如果这一行在被上一行操作影响后对于两种操作都能染白它，我们会贪心选择 ``2 * 2`` 操作，因为这个操作更可能带来更优结果。

    维护一个标签，分类讨论就能实现该逻辑。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;

            std::vector<int> a(n);
            int ans = 0, l = 0;

            for (int i = 0; i < n; i++) {
                std::cin >> a[i];

                if (a[i]) {
                    if (l == 1 && a[i] <= 2) {
                        l = 0;
                    } else if (a[i] <= 2) {
                        ans++;
                        l = 1;
                    } else if (l == 1 && a[i] <= 4) {
                        ans++;
                        l = 3;
                    } else if (l == 3 && a[i] <= 4) {
                        ans++;
                        l = 1;
                    } else {
                        ans++;
                        l = 0;
                    }
                } else {
                    l = 0;
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
