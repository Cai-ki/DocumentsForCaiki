`Increasing Sequence with Fixed OR <https://codeforces.com/contest/1988/problem/C>`_
================================================================================================

    显然，对于我们构造的序列，最后一项应该为 ``n`` 。

    把 ``n`` 进行二进制拆解， ``0`` 的位置会使之后构造的所有数字的这一位都为 ``0`` 。

    也就是说，我们只能更改 ``1`` 出现的位置。

    具体实现如下，遗憾的是鄙人证明不出来我的这种感性的写法。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            i64 n;
            std::cin >> n;

            int len = std::__lg(n) + 1;
            std::vector<i64> ans = {n};

            for (int i = 0; i < len; i++) {
                if ((n >> i) & 1) {
                    i64 x = n ^ (1LL << i);
                    if (x) ans.push_back(x);
                }
            }

            std::reverse(ans.begin(), ans.end());

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
