`Photoshoot for Gorillas <https://codeforces.com/contest/2000/problem/E>`_
==================================================================================

    很显然假设某个位置放置猩猩，这个位置的贡献可以算出来。

    开个长度为 ``k`` 的滑动窗口，分别对两维进行差分前缀和就行。

    我们得到了单个维度层面下每个下标被 ``k`` 长度窗口覆盖次数。

    计算某个下标被二维覆盖的次数就用这两个维度相乘就行。

    这时就可以贪心方式猩猩了。

    越高的猩猩就越应该放到被覆盖次数多的坐标。

    之后排序，求贡献就行了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        void solve() {
            int n, m, k;
            std::cin >> n >> m >> k;

            std::vector<std::vector<int>> cnt(n + 1, std::vector<int>(m + 1, 0)),
                a(n + 1, std::vector<int>(m + 1, 0)), p;

            std::vector<int> N(n + 2, 0), M(m + 2, 0);

            for (int i = 1; i + k - 1 <= n; i++) {
                N[i]++;
                N[i + k]--;
            }

            for (int i = 1; i + k - 1 <= m; i++) {
                M[i]++;
                M[i + k]--;
            }

            for (int i = 1; i <= n; i++) {
                N[i] += N[i - 1];
            }

            for (int i = 1; i <= m; i++) {
                M[i] += M[i - 1];
            }

            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= m; j++) {
                    cnt[i][j] = N[i] * M[j];
                    p.push_back({cnt[i][j], i, j});
                }
            }

            std::sort(p.begin(), p.end());

            int w;
            std::cin >> w;

            std::vector<int> b;

            while (w--) {
                int x;
                std::cin >> x;
                b.push_back(x);
            }

            std::sort(b.begin(), b.end(), std::greater<int>());

            int ans = 0;

            for (auto it : b) {
                auto x = p.back();
                ans += cnt[x[1]][x[2]] * it;
                p.pop_back();
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