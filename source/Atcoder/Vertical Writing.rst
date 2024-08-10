`Vertical Writing <https://atcoder.jp/contests/abc366/tasks/abc366_b>`_
==============================================================================

    签到。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int n;
            std::cin >> n;
            std::vector<std::string> a(n);

            int m = 0;
            for (auto &it : a) {
                std::cin >> it;
                m = std::max(m, (int)it.size());
            }

            for (auto &it : a) {
                it += std::string(m - it.size(), '*');
            }

            for (int j = 0; j < m; j++) {
                std::string s = "";
                for (int i = n - 1; i >= 0; i--) {
                    s += a[i][j];
                }
                while (s.size() && s.back() == '*') {
                    s.pop_back();
                }
                std::cout << s << '\n';
            }

            return 0;
        }