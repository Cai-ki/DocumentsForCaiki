`Basil's Garden <https://codeforces.com/problemset/problem/1987/C>`_
=========================================================================

    显然，最右侧花变为 :math:`0` 会用时 :math:`h_n` ，考虑倒着递推。

    假设当前为第 :math:`i` 朵花，那么其和第 :math:`i + 1` 朵花只有两种情况，第一种是两者高度不会在某一轮相等，第二种是会在某一轮相等。

    前者意味着第 :math:`i` 朵花高度更高，也意味着第 :math:`i` 朵花高度变为 :math:`0` 会用时 :math:`h_i` 。

    后者意味着两朵花会在某一个时刻高度相同（不为 :math:`0` ），并且第 :math:`i` 朵花会晚于第 :math:`i` 朵花一秒变为 :math:`0` 。

    综上， :math:`t_i` 只会等于 :math:`h_i` 或者 :math:`t_{i+1}+1` 。

    至于具体是哪一个，则是选择它们的最大的那个，因为选择其中较小的值的话显然会矛盾。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> h(n);

            for (auto &it : h) {
                std::cin >> it;
            }

            int ans = h[n - 1];

            for (int i = n - 2; i >= 0; i--) {
                ans = std::max(ans + 1, h[i]);
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