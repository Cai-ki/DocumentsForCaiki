`Maximize the Root <https://codeforces.com/contest/1997/problem/D>`_
============================================================================

    懒得想那么多，就直接二分了。

    二分答案， ``dfs`` 维护一个 ``del`` ，如果当前节点小于 ``del`` 意味着需要向儿子借值， ``del`` 加上需要多借的部分往下递归即可。

    到叶子节点就看看 ``del`` 是不是负数。

    正解可以直接 ``dfs`` 一步到位，我这个多个 ``log`` 。

    但是确实无脑。

    注意 ``del`` 增长速度很快可能会爆，记得加个限制特判一下。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            std::vector<std::vector<int>> eg(n);

            for (int i = 1; i <= n - 1; i++) {
                int j;
                std::cin >> j;
                --j;
                eg[i].push_back(j);
                eg[j].push_back(i);
            }

            i64 l = a[0], r = 1e10;

            auto check = [&](i64 x) -> bool {
                bool ok = true;
                auto dfs = [&](auto &dfs, int u, int fa, i64 del) -> void {
                    if ((!ok) || (eg[u].size() == 1 && a[u] < del) || del > 1e9) {
                        ok = false;
                        return;
                    }
                    if (del > a[u]) {
                        del += del - a[u];
                    }
                    for (auto it : eg[u]) {
                        if (it == fa) continue;
                        dfs(dfs, it, u, del);
                    }
                };

                for (auto it : eg[0]) {
                    dfs(dfs, it, 0, x - a[0]);
                }
                return ok;
            };

            while (l < r) {
                i64 mid = (l + r + 1) >> 1;

                if (check(mid)) {
                    l = mid;
                } else {
                    r = mid - 1;
                }
            }

            std::cout << l << '\n';
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