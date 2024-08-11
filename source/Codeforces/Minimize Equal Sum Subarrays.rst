`Minimize Equal Sum Subarrays <https://codeforces.com/contest/1998/problem/B>`_
=================================================================================

    这题纯找性质了。

    我们发现如果将数字循环移动一位，同一个位置的数字不会相同。

    对于任意连续区间，这个区间的和也必定不同。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            for (int i = 1; i < n; i++) {
                std::cout << a[i] << ' ';
            }
            std::cout << a[0] << '\n';
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