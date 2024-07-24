`The Omnipotent Monster Killer <https://codeforces.com/contest/1988/problem/D>`_
=========================================================================================

    根据题目描述，可以得到一个重要信息，任意两个相邻的怪物不能在同一轮被杀死。

    可以想到，总论次不会过于多，因为会通过尽快杀怪物来减少血量的损耗。

    举例一些非最优决策，比如在第一次杀一半怪兽，第二次就全部杀完。

    或者每次贪心选择伤害最大的怪兽，直到不能再选后把这一堆杀完，重复以上过程。

    感性来想，轮数不应该超过 ``log`` 量级。

    以此为核心，可以尝试用动态规划来解决问题。

    设计状态： ``dp[u][i]`` ，代表第 ``u`` 个点在第 ``i`` 轮被消灭时，以 ``u`` 为根节点的子树的贡献。

    对于任何一个怪兽来说，它只会被四周的怪兽的状态所影响，因此可以进行以下状态转移。

    :math:`dp[u][i] = \sum_{(u,v) \subset E} \min_{i \ne j} dp[v][j] + a[u] * i`

    具体实现就比较简单了，时间复杂度为 :math:`n\log^{2}n`

    当然，理论层面上 :math:`n\log n` 甚至 :math:`n` 的时间复杂度的写法都存在，其实都是依据度数层面的性质上做优化，这里不多做赘述。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n;
            std::cin >> n;
            std::vector<i64> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            std::vector<std::vector<int>> eg(n);

            for (int i = 0; i < n - 1; i++) {
                int u, v;
                std::cin >> u >> v;
                u--, v--;
                eg[u].push_back(v);
                eg[v].push_back(u);
            }

            std::vector<std::vector<i64>> dp(n, std::vector<i64>(25));

            auto dfs = [&](auto &dfs, int u, int fa) -> void {
                for (int i = 1; i < 25; i++) {
                    dp[u][i] = a[u] * i;
                }

                for (auto it : eg[u]) {
                    if (it == fa) continue;

                    dfs(dfs, it, u);
                    for (int i = 1; i < 25; i++) {
                        i64 mn = 1e18;
                        for (int j = 1; j < 25; j++) {
                            if (i == j) continue;
                            mn = std::min(mn, dp[it][j]);
                        }
                        dp[u][i] += mn;
                    }
                }
            };

            dfs(dfs, 0, -1);

            std::cout << *std::min_element(dp[0].begin() + 1, dp[0].end()) << '\n';
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