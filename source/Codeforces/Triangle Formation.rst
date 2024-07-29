`Triangle Formation <https://codeforces.com/contest/1991/problem/F>`_
========================================================================

    可以发现一个性质。

    如果这个区间是个斐波那契数列，显然无解。

    但由于数组的最大为 ``1e9`` ，那么如果这个区间内大于 ``50`` 是必定有解。

    接下来就可以暴力特判了。

    首先统计挨着的能凑成合法三角形的数量（挨着的更可能凑成合法三角形）。

    接下来讨论特殊情况。

    有可能挨着的 ``6`` 个能凑成两个合法三角形，但是单个三角形的木棍长度并不是相邻的。

    特判所有情况即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int n, q;
            std::cin >> n >> q;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            while (q--) {
                int l, r;
                std::cin >> l >> r;
                --l, --r;

                if (r - l + 1 >= 50) {
                    std::cout << "YES\n";
                    continue;
                }

                std::vector<int> b(a.begin() + l, a.begin() + r + 1);
                std::sort(b.begin(), b.end());

                int cnt = 0;
                for (int i = 0; i + 2 < b.size(); i++) {
                    if (b[i] + b[i + 1] > b[i + 2]) {
                        if ((++cnt) >= 2) break;
                        i += 2;
                    }
                }

                if (cnt >= 2) {
                    std::cout << "YES\n";
                    continue;
                }

                bool ok = false;

                for (int i = 0; i + 5 < b.size() && !ok; i++) {
                    if (b[i] + b[i + 1] > b[i + 3] && b[i + 2] + b[i + 4] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i] + b[i + 2] > b[i + 3] && b[i + 1] + b[i + 4] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i + 1] + b[i + 2] > b[i + 3] && b[i] + b[i + 4] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i] + b[i + 1] > b[i + 4] && b[i + 2] + b[i + 3] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i] + b[i + 2] > b[i + 4] && b[i + 1] + b[i + 3] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i + 1] + b[i + 2] > b[i + 4] && b[i] + b[i + 3] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                    if (b[i] + b[i + 3] > b[i + 4] && b[i + 1] + b[i + 2] > b[i + 5]) {
                        ok = true;
                        break;
                    }
                }

                std::cout << (ok ? "YES\n" : "NO\n");
            }
            return 0;
        }
