`Connect the Dots <https://codeforces.com/contest/2020/problem/D>`_
==========================================================================

    思路很简单，仔细阅读题目可以发现 ``d`` 的数据范围很小。

    可以考虑 ``DP + DSU`` 。

    设计 ``DP`` 状态， ``f[a][d]`` 表示位置 ``a`` 最多和右侧多少个位置（ ``d`` 为间距）相连。 

    转移的时候判断旧的状态能不能和当前位置建边，动态 ``merge`` 即可。

    我的评价是十分 ``easy`` 。

    .. code-block:: CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <algorithm>
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

        struct DSU {
            std::vector<int> f, siz;
            int cnt;

            DSU() {}
            DSU(int n) { init(n); }

            void init(int n) {
                f.resize(n);
                std::iota(f.begin(), f.end(), 0);
                siz.assign(n, 1);
                cnt = n;
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
                cnt--;
                return true;
            }

            int size(int x) { return siz[find(x)]; }
        };

        void solve() {
            int n, m;
            std::cin >> n >> m;
            std::vector<std::vector<int>> f(n + 1, std::vector<int>(11, 0));

            DSU dsu(n + 1);

            while (m--) {
                int a, d, k;
                std::cin >> a >> d >> k;
                f[a][d] = std::max(f[a][d], k);
            }

            for (int i = 1; i <= n; i++) {
                for (int d = 1; d <= 10; d++) {
                    int j = i - d;
                    if (j < 1) {
                        continue;
                    }
                    if (f[j][d]) {
                        dsu.merge(i, j);
                    }
                    f[i][d] = std::max(f[i][d], f[j][d] - 1);
                }
            }

            std::cout << dsu.cnt - 1 << '\n';
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