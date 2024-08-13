`DFS Checker (Hard Version) <https://codeforces.com/contest/2002/problem/D2>`_
=================================================================================

    对于所有操作显然不能暴力判断是否合法，往优化暴力的角度上去思考也是不可取的。

    其实可以想一下当前序列满足 ``DFS`` 序列的充要条件。

    我们可以先寻找一些共性。

    对于当前序列，只取相邻的两个元素，若当前序列是 ``DFS`` 序列，这两个元素会有什么特点？

    显然，第一个特点是后一个元素一定不是前一个元素的祖先，这不需要怎么解释， ``DFS`` 的特性罢了。

    第二个特点是后一个元素的父亲一定是前一个元素的祖先（也可能后一个元素的父亲就是前一个元素）。

    这里就需要大胆猜想，假如所有相邻情况都满足以上特点，这个序列一定是 ``DFS`` 序列。

    题目的交换操作影响的相邻情况并不多，也就是说其它没影响到的情况的正确性可以继承下去。

    我们可以维护满足情况的数量，如果所有情况都满足即数量为 ``n - 1`` 当前序列合法。

    判断祖先关系可以手撸 ``LCA`` ，也可以预处理 ``DFN`` 序来判断。下方代码是偷的 ``HLD`` 板子。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        struct HLD {
            int n;
            std::vector<int> siz, top, dep, parent, in, out, seq;
            std::vector<std::vector<int>> adj;
            int cur;

            HLD() {}
            HLD(int n) { init(n); }
            void init(int n) {
                this->n = n;
                siz.resize(n);
                top.resize(n);
                dep.resize(n);
                parent.resize(n);
                in.resize(n);
                out.resize(n);
                seq.resize(n);
                cur = 0;
                adj.assign(n, {});
            }
            void addEdge(int u, int v) {
                adj[u].push_back(v);
                adj[v].push_back(u);
            }
            void work(int root = 0) {
                top[root] = root;
                dep[root] = 0;
                parent[root] = -1;
                dfs1(root);
                dfs2(root);
            }
            void dfs1(int u) {
                if (parent[u] != -1) {
                    adj[u].erase(std::find(adj[u].begin(), adj[u].end(), parent[u]));
                }

                siz[u] = 1;
                for (auto &v : adj[u]) {
                    parent[v] = u;
                    dep[v] = dep[u] + 1;
                    dfs1(v);
                    siz[u] += siz[v];
                    if (siz[v] > siz[adj[u][0]]) {
                        std::swap(v, adj[u][0]);
                    }
                }
            }
            void dfs2(int u) {
                in[u] = cur++;
                seq[in[u]] = u;
                for (auto v : adj[u]) {
                    top[v] = v == adj[u][0] ? top[u] : v;
                    dfs2(v);
                }
                out[u] = cur;
            }
            int lca(int u, int v) {
                while (top[u] != top[v]) {
                    if (dep[top[u]] > dep[top[v]]) {
                        u = parent[top[u]];
                    } else {
                        v = parent[top[v]];
                    }
                }
                return dep[u] < dep[v] ? u : v;
            }

            int dist(int u, int v) { return dep[u] + dep[v] - 2 * dep[lca(u, v)]; }

            int jump(int u, int k) {
                if (dep[u] < k) {
                    return -1;
                }

                int d = dep[u] - k;

                while (dep[top[u]] > d) {
                    u = parent[top[u]];
                }

                return seq[in[u] - dep[u] + d];
            }

            bool isAncester(int u, int v) { return in[u] <= in[v] && in[v] < out[u]; }

            int rootedParent(int u, int v) {
                std::swap(u, v);
                if (u == v) {
                    return u;
                }
                if (!isAncester(u, v)) {
                    return parent[u];
                }
                auto it =
                    std::upper_bound(adj[u].begin(), adj[u].end(), v,
                                    [&](int x, int y) { return in[x] < in[y]; }) -
                    1;
                return *it;
            }

            int rootedSize(int u, int v) {
                if (u == v) {
                    return n;
                }
                if (!isAncester(v, u)) {
                    return siz[v];
                }
                return n - siz[rootedParent(u, v)];
            }

            int rootedLca(int a, int b, int c) {
                return lca(a, b) ^ lca(b, c) ^ lca(c, a);
            }
        };

        void solve() {
            int n, q;
            std::cin >> n >> q;

            HLD t(n);
            std::vector<int> a(n), p(n);

            for (int i = 1; i < n; i++) {
                std::cin >> a[i];
                --a[i];
                t.addEdge(a[i], i);
            }

            t.work();

            for (auto &it : p) {
                std::cin >> it;
                --it;
            }

            int cnt = 0;
            std::vector<bool> good(n);

            auto check = [&](int u) -> void {
                if (u < 1 || u >= n) {
                    return;
                }
                cnt -= good[u];
                good[u] =
                    (!t.isAncester(p[u], p[u - 1])) && t.isAncester(a[p[u]], p[u - 1]);
                cnt += good[u];
            };

            for (int i = 1; i < n; i++) {
                check(i);
            }

            while (q--) {
                int x, y;
                std::cin >> x >> y;
                --x, --y;
                std::swap(p[x], p[y]);
                check(x);
                check(x + 1);
                check(y);
                check(y + 1);
                if (cnt == n - 1) {
                    std::cout << "YES\n";
                } else {
                    std::cout << "NO\n";
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