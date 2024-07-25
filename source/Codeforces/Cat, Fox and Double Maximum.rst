`Cat, Fox and Double Maximum <https://codeforces.com/contest/1973/problem/C>`_
========================================================================================

    显然，我们无论如何构造，得到的分数上限就是 ``n / 2 - 1`` 。

    可以通过下标奇偶来将数组分为两个部分。

    接下来将 ``n / 2 + 1 ~ n`` 都分给包含 ``n`` 的那个部分。

    剩下的数字分给另外的部分。

    对于同一部分，当前值越小，分得的数字越大，这样可以保证和至少为 ``n + 1`` 。

    另一部分同理，同样可以保证和最多为 ``n`` 。

    这样构造得分必定为 ``n / 2 - 1`` 。

    通过这样就能构造出最优答案，。

    对于 ``n`` 在奇偶的不同位置可以通过反转数组来变成一种情况讨论，不过要记得保存好实际下标。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            int pos = std::find(a.begin(), a.end(), n) - a.begin();

            if (pos & 1) std::reverse(a.begin(), a.end());

            std::vector<int> ans(n);
            std::vector<std::pair<int, int>> v;

            for (int i = 1; i < n; i += 2) {
                v.push_back({a[i], ((pos & 1) ? n - i - 1 : i)});
            }

            for (int i = 0; i < n; i += 2) {
                v.push_back({a[i], ((pos & 1) ? n - i - 1 : i)});
            }

            std::sort(v.begin(), v.begin() + n / 2,
                    std::greater<std::pair<int, int>>());
            std::sort(v.begin() + n / 2 + 1, v.begin() + n,
                    std::greater<std::pair<int, int>>());

            for (int i = 0; i < n; i++) {
                ans[v[i].second] = i + 1;
            }

            for (auto it : ans) {
                std::cout << it << ' ';
            }
            std::cout << '\n';
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