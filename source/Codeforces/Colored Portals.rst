`Colored Portals <https://codeforces.com/contest/2004/problem/D>`_
==========================================================================

    预处理每个位置左侧最靠近的某种城市位置和右侧最靠近的某种城市位置。

    乱搞就行了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        constexpr int N = 2e5 + 10, INF = 1e9;

        int l[N][4][4], r[N][4][4];

        void solve() {
            int n, q;
            std::cin >> n >> q;

            std::vector<std::string> a(n + 1);

            for (int i = 1; i <= n; i++) {
                std::cin >> a[i];
            }

            std::vector<int> id(256);
            id['B'] = 0;
            id['G'] = 1;
            id['R'] = 2;
            id['Y'] = 3;

            for (int i = 0; i <= n + 1; i++) {
                for (int u = 0; u < 4; u++) {
                    for (int v = 0; v < 4; v++) {
                        r[i][u][v] = INF;
                        l[i][u][v] = INF;
                    }
                }
            }

            for (int i = 1; i <= n; i++) {
                int x = id[a[i][0]], y = id[a[i][1]];
                for (int u = 0; u < 4; u++) {
                    for (int v = 0; v < 4; v++) {
                        l[i][u][v] = l[i - 1][u][v];
                    }
                }
                l[i][x][y] = i;
                l[i][y][x] = i;
            }

            for (int i = n; i >= 1; i--) {
                int x = id[a[i][0]], y = id[a[i][1]];
                for (int u = 0; u < 4; u++) {
                    for (int v = 0; v < 4; v++) {
                        r[i][u][v] = r[i + 1][u][v];
                    }
                }
                r[i][x][y] = i;
                r[i][y][x] = i;
            }

            while (q--) {
                int x, y;
                std::cin >> x >> y;

                int ans = INF;
                for (int i = 0; i < 2; i++) {
                    for (int j = 0; j < 2; j++) {
                        int u = id[a[x][i]], v = id[a[y][j]];
                        if (l[x][u][v] != INF) {
                            ans = std::min(ans,
                                        abs(x - l[x][u][v]) + abs(y - l[x][u][v]));
                        }
                        if (r[x][u][v] != INF) {
                            ans = std::min(ans,
                                        abs(x - r[x][u][v]) + abs(y - r[x][u][v]));
                        }
                        if (l[y][u][v] != INF) {
                            ans = std::min(ans,
                                        abs(x - l[y][u][v]) + abs(y - l[y][u][v]));
                        }
                        if (r[y][u][v] != INF) {
                            ans = std::min(ans,
                                        abs(x - r[y][u][v]) + abs(y - r[y][u][v]));
                        }
                    }
                }
                if (ans >= INF) ans = -1;

                std::cout << ans << '\n';
            }
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