`Rendez-vous de Marian et Robin <https://codeforces.com/contest/2014/problem/E>`_
=======================================================================================

    经典分层图最短路，建两层图，层与层之间用边权为 ``0`` 的边连一下，跑个最短路就行了。

    过于简单了。

    .. code-block:: CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <iostream>
        #include <queue>
        #include <vector>

        auto _ = []() {
            freopen("../io/in.txt", "r", stdin);
            freopen("../io/out.txt", "w", stdout);
            return true;
        }();

        #endif

        #define int long long

        class Dij {
        public:
            int n, _INF = 1e16;
            std::vector<std::vector<std::pair<int, int>>> eg;
            std::vector<int> dist, vis;

            void dij(int st) {
                dist.insert(dist.begin(), n + 1, _INF);
                vis.insert(vis.begin(), n + 1, 0);
                dist[st] = 0;
                std::priority_queue<std::pair<int, int>,
                                    std::vector<std::pair<int, int>>,
                                    std::greater<std::pair<int, int>>>
                    heap;

                heap.push({dist[st], st});
                while (heap.size()) {
                    auto [dis, vir] = heap.top();
                    heap.pop();

                    if (vis[vir]) continue;
                    vis[vir] = 1;

                    for (auto [it, w] : eg[vir]) {
                        if (dist[it] > dist[vir] + w) {
                            dist[it] = dist[vir] + w;
                            heap.push({dist[it], it});
                        }
                    }
                }
            }

            Dij(int in) : n(in), eg(in + 1), dist(in + 1), vis(in + 1) {};
        };

        void solve() {
            int n, m, h;
            std::cin >> n >> m >> h;

            std::vector<int> a(h);

            for (auto &it : a) {
                std::cin >> it;
            }

            Dij d(2 * n);

            while (m--) {
                int u, v, w;
                std::cin >> u >> v >> w;

                d.eg[u].push_back({v, w});
                d.eg[v].push_back({u, w});
                d.eg[u + n].push_back({v + n, w / 2});
                d.eg[v + n].push_back({u + n, w / 2});
            }

            for (auto it : a) {
                d.eg[it].push_back({it + n, 0});
                d.eg[it + n].push_back({it, 0});
            }

            d.dij(1);
            auto dist1 = d.dist;
            d.dij(n);
            auto dist2 = d.dist;

            int ans = 1e16;
            for (int i = 1; i <= n; i++) {
                ans = std::min(ans, std::max(dist1[i], dist2[i]));
                ans = std::min(ans, std::max(dist1[i + n], dist2[i]));
                ans = std::min(ans, std::max(dist1[i], dist2[i + n]));
                ans = std::min(ans, std::max(dist1[i + n], dist2[i + n]));
            }

            if (ans >= 1e16) {
                std::cout << -1 << '\n';
            } else {
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