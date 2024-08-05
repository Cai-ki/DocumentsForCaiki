`Portals <https://codeforces.com/problemset/problem/1271/D>`_
===================================================================

    显然可以发现，如果一些单向边指向同一个城市，完全可以将这些单向边缩减成一条单向边，即只取起始点 id 更大的那条边。

    不需要多麻烦解释，由于城市是顺序访问，对于某个城市如果你想守护它，不如越靠后越好，过早守护可能会导致后面的城市攻占不下来，运用贪心的思想很容易理解。

    我们会使用动态规划对这题进行求解。

    设计状态 :math:`dp_{i,k}` ，表示第 i 轮拥有 k 个士兵时能得到的最大收益。

    易得 :math:`dp_{i,k} = \begin{cases} dp_{i-1,k-b[i]} , k-b[i] \ge a[i]\\ -1 , k-b[i]<a[i] \end{cases}`

    当然，这一轮还未结束，我们完全可以将手中过多的士兵派走从而获得更多收益。

    因此 :math:`dp_{i,k} =  \begin{cases} \max (dp_{i,k+1} + c[j]), dp_{i,k+1} \ne -1 \\ do\ nothing , otherwise \end{cases} ,  j \in e[i]`


    .. code-block:: CPP

        #include <bits/stdc++.h>

        constexpr int N = 5000;

        std::vector<int> a(N), b(N), c(N), dp(N + 1, -1), r(N), e[N];

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int n, m, k;
            std::cin >> n >> m >> k;

            for (int i = 0; i < n; i++) {
                std::cin >> a[i] >> b[i] >> c[i];
            }

            std::iota(r.begin(), r.end(), 0);

            while (m--) {
                int u, v;
                std::cin >> u >> v;
                --u, --v;
                r[v] = std::max(r[v], u);
            }

            for (int i = 0; i < n; i++) {
                if (r[i] > -1) {
                    e[r[i]].push_back(i);
                }
            }

            dp[k] = 0;

            for (int i = 0; i < n; i++) {
                for (int j = N - b[i]; j >= a[i]; j--) {
                    dp[j + b[i]] = dp[j];
                }

                for (int j = 0; j < a[i] + b[i]; j++) {
                    dp[j] = -1;
                }

                for (int it : e[i]) {
                    for (int j = 0; j < N; j++) {
                        if (dp[j + 1] != -1) {
                            dp[j] = std::max(dp[j], dp[j + 1] + c[it]);
                        }
                    }
                }
            }

            std::cout << *std::max_element(dp.begin(), dp.end()) << '\n';

            return 0;
        }