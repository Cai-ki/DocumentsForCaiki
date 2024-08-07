`Feed Cats <https://codeforces.com/contest/1932/problem/F>`_
====================================================================

    发现选择一个时间点喂猫只会影响有限只猫咪，因此也只会在意有限范围。

    考虑动态规划。

    思考对于每个时间点我们喂猫决策的影响。

    喂猫，则意味着我们会收获该时间点覆盖猫个数的贡献加上之前喂猫得到的贡献，但是由于重叠问题我们加得的贡献只能是不能包含当前时间点包含的猫的贡献。

    不喂猫，意味着当前操作没有任何贡献，也就是说可以继承上个时间点的答案。

    现在状态转移的方式其实很明显了。

    设 :math:`dp_i` 为走到当前步时的最优解。

    则 :math:`dp_i = \max (cnt_i + dp_{f_i-1},dp_{i-1})` 。

    当然我们需要提前预处理得每个时间点能覆盖的猫咪的个数，和每个时间点与其它时间点发生冲突的最早时间，这些都很容易求出，这里不做解释。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, m;
            std::cin >> n >> m;

            std::vector<int> cnt(n + 1, 0), f(n + 1, n + 1);
            while (m--) {
                int l, r;
                std::cin >> l >> r;
                f[r] = std::min(f[r], l);
                cnt[l]++;
                if (r + 1 <= n) {
                    cnt[r + 1]--;
                }
            }

            for (int i = 1; i <= n; i++) {
                cnt[i] += cnt[i - 1];
            }

            for (int i = n - 1; i >= 1; i--) {
                f[i] = std::min(f[i], f[i + 1]);
            }

            std::vector<int> dp(n + 1, 0);

            for (int i = 1; i <= n; i++) {
                dp[i] = cnt[i] + dp[f[i] - 1];
                if (i > 1) {
                    dp[i] = std::max(dp[i], dp[i - 1]);
                }
            }

            std::cout << dp[n] << '\n';
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