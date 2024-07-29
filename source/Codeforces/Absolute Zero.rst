`Absolute Zero <https://codeforces.com/contest/1991/problem/C>`_
======================================================================

    显然可以发现如果数组奇偶性不一样一定无解。

    根据题目描述，最多可以进行 ``40`` 次操作，所有元素最大为 ``1e9`` 。

    而 :math:`log_{2}(10^{9})` 才 ``20`` 左右。

    因此我们可以重复 ``40`` 次操作，每次选择 ``x`` 为最大最小值和的一半。

    这样能保证整个数组的最大值每次至少减少一半。

    ``40`` 次操作肯定够用。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            int cnt = 0;

            for (auto &it : a) {
                std::cin >> it;
                cnt += it & 1;
            }

            if (cnt != 0 && cnt != n) {
                std::cout << "-1\n";
                return;
            }

            auto get = [&]() -> int {
                int x = (*std::max_element(a.begin(), a.end()) +
                        *std::min_element(a.begin(), a.end())) /
                        2;
                for (auto &it : a) {
                    it = std::abs(it - x);
                }
                return x;
            };

            std::vector<int> ans;

            for (int i = 0; i < 40; i++) {
                int x = get();
                if (x) ans.push_back(x);
            }

            std::cout << ans.size() << '\n';
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