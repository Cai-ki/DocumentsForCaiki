`Funny Game <https://codeforces.com/contest/1994/problem/D>`_
=================================================================

    很有意思的一道题。

    假如我们倒着进行操作，并使用 ``DSU`` 维护连通块，这时可以发现一个性质。

    在第 ``x - 1`` 次操作时，我们手中必定会存在 ``x`` 个连通块。

    此时 ``a[] % (x - 1)`` 相等的点能够相互连边，这个值域的大小为 ``x - 1`` 。

    但我们有 ``x`` 个连通块，所以必定可以找到一条新的边连接某两个连通块。

    为了防止重边，我们只允许某个连通块的祖宗连边（其实谁连都一样，只不过祖宗好找罢了，而且非祖宗节点不会考虑，就不会有重边）。

    遵守这样的规则，无论如何都能构造出答案。

    .. code-block:: CPP

        #include <bits/stdc++.h>

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
            int n;
            std::cin >> n;

            std::vector<int> a(n);
            for (int i = 0; i < n; i++) {
                std::cin >> a[i];
            }

            DSU dsu(n);
            std::vector<int> u(n), v(n);
            for (int x = n - 1; x >= 1; x--) {
                std::vector<int> b(x, -1);
                for (int i = 0; i < n; i++) {
                    if (dsu.find(i) != i) {
                        continue;
                    }
                    if (b[a[i] % x] != -1) {
                        u[x] = b[a[i] % x];
                        v[x] = i;
                        dsu.merge(u[x], v[x]);
                        break;
                    }
                    b[a[i] % x] = i;
                }
            }
            std::cout << "YES\n";
            for (int i = 1; i < n; i++) {
                std::cout << u[i] + 1 << " " << v[i] + 1 << "\n";
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