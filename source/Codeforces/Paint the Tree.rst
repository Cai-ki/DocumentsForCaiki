`Paint the Tree <https://codeforces.com/contest/1975/problem/D>`_
=======================================================================

    这个题目的理解比较感性，整体上就是尽快让两棋子相遇，然后走完全图。

    首先我们需要知道，假如单纯只是一个点的染色问题，答案是什么？

    答案是 ``2 * (n - 1) - d`` ，这里的 ``d`` 是距离初始点最长的到叶子节点的路径长。

    由于是在一棵树上行走，显然一条边要经过两次，去一次回来一次，当然这是对于有多个分岔的情况。

    而且最后一趟不会走回头路，那么肯定是选择最长的那个岔道留到最后走，因为会省去走相同长度回头路的路程，也就是说会减去 ``d`` 。

    有了这个前提，我们就可以感性的想到，答案应该为 ``2 * (n - 1) - d + step`` 这里的 ``step`` 是让两点相遇最少需要的步数。

    相遇的两点有两种情况，第一种是停在同一点，第二种是相遇后停在不同点，不论如何我们只需要知道相遇后最先变蓝的那个点的位置就行。

    实现就很简单了，找出需要的点，以此为基础找出距离该点最长的路径长，计算答案即可。

    感性上来证明该思路的合理性就是，如果不以最快速度相遇，其它方案都会浪费相遇时间从而导致答案不会更优。

    :math:`P_{b}` 棋子肯定是需要跟在 :math:`P_{a}` 棋子走过的路径上走的，如果 :math:`P_{a}` 和 :math:`P_{b}` 不尽快相遇，:math:`P_{b}` 就需要白白浪费时间走更多的白色的点。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, a, b;
            std::cin >> n >> a >> b;
            --a, --b;
            std::vector<std::vector<int>> eg(n);

            for (int i = 0; i < n - 1; ++i) {
                int u, v;
                std::cin >> u >> v;
                --u, --v;
                eg[u].push_back(v);
                eg[v].push_back(u);
            }

            std::vector<int> deep(n), father(n);

            auto dfs = [&](auto &dfs, int u, int fa) -> void {
                father[u] = fa;

                for (auto it : eg[u]) {
                    if (it == fa) {
                        continue;
                    }
                    deep[it] = deep[u] + 1;
                    dfs(dfs, it, u);
                }
            };

            deep[a] = 0;
            dfs(dfs, a, a);

            int center = b, step = 0;

            while (deep[center] * 2 > deep[b]) {
                center = father[center];
                ++step;
            }

            deep[center] = 0;
            dfs(dfs, center, center);

            std::cout << 2 * (n - 1) + step -
                            *std::max_element(deep.begin(), deep.end())
                    << '\n';
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