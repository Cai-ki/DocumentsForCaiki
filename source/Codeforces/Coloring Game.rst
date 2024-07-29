`Coloring Game <https://codeforces.com/contest/1991/problem/E>`_
=======================================================================

    发现如果存在奇数环，则后手必输（先手只需要全输出 ``1 2`` 即可）。

    考虑不存在奇数环的情况。

    显然后手有必胜策略。

    首先我们需要把所有点分成两堆，要保证两堆的颜色互不相同，堆内部无所谓。

    我们发现总共可选的颜色为 ``1 2 3`` ，每次还会给两个不同颜色，这意味着什么？

    意味着假如我们固定两堆的颜色就是 ``1 2`` ，在任意一堆填满之前，我们都能得到需要的 ``1`` 或 ``2`` 。

    这样一定能先填满一个堆。

    剩下的就简单了，把 ``3`` 号颜色分给非空堆就行，因为不论如何，我们都可以在给出的颜色中找到接下来所需要的两种颜色。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, m;
            std::cin >> n >> m;
            std::vector<std::vector<int>> eg(n + 1);
            while (m--) {
                int u, v;
                std::cin >> u >> v;
                eg[u].push_back(v);
                eg[v].push_back(u);
            }

            std::vector<int> id(n + 1, -1), group[2];

            bool ok = true;

            auto dfs = [&](auto &dfs, int u, int fa) -> void {
                group[id[u]].push_back(u);
                for (auto it : eg[u]) {
                    if (it == fa) continue;
                    int x = id[u] ^ 1;
                    if (id[it] == -1) {
                        id[it] = x;
                        dfs(dfs, it, u);
                    } else if (id[it] != x) {
                        ok = false;
                    }
                }
            };

            for (int i = 1; i <= n; i++) {
                if (id[i] == -1) {
                    id[i] = 1;
                    dfs(dfs, i, 0);
                }
            }

            if (!ok) {
                std::cout << "Alice\n" << std::endl;
                while (n--) {
                    std::cout << "1 2" << std::endl;
                    int x, y;
                    std::cin >> x >> y;
                }
                return;
            }

            std::cout << "Bob" << std::endl;
            while (n--) {
                int x, y;
                std::cin >> x >> y;

                if ((x == 1 || y == 1) && group[0].size()) {
                    std::cout << group[0].back() << " 1" << std::endl;
                    group[0].pop_back();
                } else if ((x == 2 || y == 2) && group[1].size()) {
                    std::cout << group[1].back() << " 2" << std::endl;
                    group[1].pop_back();
                } else if (group[0].size()) {
                    std::cout << group[0].back() << " 3" << std::endl;
                    group[0].pop_back();
                } else if (group[1].size()) {
                    std::cout << group[1].back() << " 3" << std::endl;
                    group[1].pop_back();
                }
            }
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