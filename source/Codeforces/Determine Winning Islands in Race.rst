`Determine Winning Islands in Race <https://codeforces.com/contest/1998/problem/D>`_
==========================================================================================

    唐题，不配作为 ``D`` 。

    可以发现，如果 ``B`` 会输只有可能是 ``E`` 在某个时候直接跳到 ``B`` 前面。

    也就是说最重要的是这一跳。

    因此，我们可以枚举起跳点，递推更新到当前位置需要的最少步数。

    这时 ``B`` 会输的位置也肯定在这个区间内。

    我们可以用式子算出来 ``B`` 最远会在哪个位置输即可（我不想思考直接无脑二分了）。

    ``u + dp[i] + 1`` 这个式子代表当 ``E`` 跳跃时 ``B`` 所在的位置，显然要小于我们跳到的这个点（不能等于因为 ``B`` 先跳）。

    对于区间答案更新用差分加上前缀和即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        constexpr int INF = 1e9;

        void solve() {
            int n, m;
            std::cin >> n >> m;

            std::vector<int> dp(n, INF), ans(n + 1, 0);
            std::vector<std::vector<int>> eg(n);

            while (m--) {
                int u, v;
                std::cin >> u >> v;
                --u, --v;
                eg[u].push_back(v);
            }

            dp[0] = 0;
            for (int i = 0; i < n; i++) {
                if (i) {
                    dp[i] = std::min(dp[i], dp[i - 1] + 1);
                }
                for (int j : eg[i]) {
                    dp[j] = std::min(dp[j], dp[i] + 1);
                    if (j - i <= 2) continue;

                    auto check = [&](int u) -> bool { return u + dp[i] + 1 < j; };

                    int l = i + 1, r = j - 1;
                    while (l < r) {
                        int mid = (l + r + 1) >> 1;
                        if (check(mid)) {
                            l = mid;
                        } else {
                            r = mid - 1;
                        }
                    }

                    if (check(l)) {
                        ans[i + 1]++;
                        ans[l + 1]--;
                    }
                }
            }

            for (int i = 0; i < n - 1; i++) {
                ans[i + 1] += ans[i];
                std::cout << (ans[i] > 0 ? 0 : 1);
            }
            std::cout << '\n';
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