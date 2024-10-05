`Cool, It’s Yesterday Four Times More <https://codeforces.com/group/2RVo4v5Tuc/contest/555725/problem/A>`_
======================================================================================================================

    算好复杂度后， ``bitset`` 硬推过去。

    题目其实非常简单，只需要对每个袋鼠构建以其为坐标系源点的能到达的图，将这个图转化为 ``bitset`` ，之后暴力 ``n ^ 2`` 枚举其它袋鼠，假如其它袋鼠的 ``bitset`` 包含该袋鼠，则该袋鼠不加入统计。

    时间复杂度为 :math:`O(\frac{(nm)^3}{16})` 。

    这样算复杂度其实有卡常的危险。（神奇的 ``cf`` 评测姬竟然能过？！）

    我们可以加一些优化。

    可以发现位于同一个连通块的袋鼠互相之间必能让对方去世。
    
    假如两个袋鼠位于同一个连通块，我们就不需要用 ``bitset`` 特判了。

    对于极限情况下，每个连通块都只有一个成员，这样我们的袋鼠最多有 :math:`O(\frac{nm}{2})` 个 。

    时间复杂度能优化为 :math:`O(\frac{(nm)^3}{64})` 。

    PS ：``bitset`` 不要开太接近 ``4000`` ，尽量开大点，这是因为我们在求每个袋鼠能到达的新图时，新图的大小为 ``(2 * n + 1) * (2 * m + 1)`` 实际上不能严格算为原图的整 ``4`` 倍，根据不同的 ``n`` 和 ``m`` 最大甚至能有 ``5`` 倍， ``5500`` 就很稳妥。

    .. code-block:: CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <bitset>
        #include <iostream>
        #include <numeric>
        #include <vector>

        auto _ = []() {
            freopen("../io/in.txt", "r", stdin);
            freopen("../io/out.txt", "w", stdout);
            return true;
        }();
        #endif

        #define int long long

        constexpr int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};

        struct DSU {
            std::vector<int> f, siz;

            DSU() {}
            DSU(int n) { init(n); }

            void init(int n) {
                f.resize(n);
                std::iota(f.begin(), f.end(), 0);
                siz.assign(n, 1);
            }

            int find(int x) {
                while (x != f[x]) {
                    x = f[x] = f[f[x]];
                }
                return x;
            }

            bool same(int x, int y) { return find(x) == find(y); }

            bool merge(int x, int y) {
                x = find(x);
                y = find(y);
                if (x == y) {
                    return false;
                }
                siz[x] += siz[y];
                f[y] = x;
                return true;
            }

            int size(int x) { return siz[find(x)]; }
        };

        void solve() {
            int n, m;
            std::cin >> n >> m;

            DSU d(n * m);

            std::vector<std::string> g(n);

            for (auto &it : g) {
                std::cin >> it;
            }

            std::vector<std::pair<std::bitset<5500>, std::pair<int, int>>> v;

            for (int i = 0; i < n; i++) {
                for (int j = 0; j < m; j++) {
                    if (g[i][j] == '.') {
                        std::vector<std::vector<int>> map(
                            2 * n + 1, std::vector<int>(2 * m + 1, 0));

                        auto dfs = [&](auto &dfs, int x, int y) -> void {
                            for (int k = 0; k < 4; k++) {
                                int nx = x + dx[k], ny = y + dy[k];
                                int rx = i + nx, ry = j + ny;

                                if (rx < 0 || rx >= n || ry < 0 || ry >= m ||
                                    g[rx][ry] == 'O' || nx < (-n) || nx > n ||
                                    ny < (-m) || ny > m || map[nx + n][ny + m] == 1) {
                                    continue;
                                }

                                d.merge(i * m + j, rx * m + ry);

                                map[nx + n][ny + m] = 1;
                                dfs(dfs, nx, ny);
                            }
                        };

                        map[n][m] = 1;
                        dfs(dfs, 0, 0);

                        std::bitset<5500> bit;

                        for (int x = 0; x < 2 * n + 1; x++) {
                            for (int y = 0; y < 2 * m + 1; y++) {
                                bit[x * (2 * m + 1) + y] = map[x][y];
                            }
                        }

                        v.push_back({bit, {i, j}});
                    }
                }
            }

            int ans = 0;

            for (auto [bit1, p1] : v) {
                bool yes = true;

                for (auto [bit2, p2] : v) {
                    if (d.find(p1.first * m + p1.second) ==
                        d.find(p2.first * m + p2.second)) {
                        continue;
                    }
                    if ((bit1 & bit2) == bit1) {
                        yes = false;
                        break;
                    }
                }

                ans += yes;
            }

            std::cout << ans << '\n';
        }

        signed main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int t;
            std::cin >> t;

            while (t--) {
                solve();
            }

            return 0;
        }