`Decode <https://codeforces.com/contest/1996/problem/E>`_
===============================================================

    试想一下，对于一个满足题目要求的区间来说，它对答案的贡献怎么计算？

    计算它的贡献，其实是计算有多少区间包含它。

    假如这个区间是 ``l`` 到 ``r`` （下标从 ``1`` 开始） ，显然贡献应该是 ``l * (n - r + 1)`` 。

    但是假如有多个 ``l`` 呢？那么贡献应该是 ``n - r + 1`` 乘上多个 ``l`` 的和。

    知道这个问题其实就已经解决了。

    我们枚举 ``r`` ，并维护与该 ``r`` 所配对的 ``l`` 的和。每次我们都能把以该 ``r`` 为结尾的区间的贡献加到答案里。

    计算完成后，我们的 ``r`` 将作为新的 ``l - 1`` 来加入我们所维护的信息中。

    由此我们可以算出 ``l`` 并加入维护的 ``map`` 里， ``map`` 的 ``key`` 自然是我们维护的前缀和的值。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            std::string s;
            std::cin >> s;

            std::map<int, i64> map;
            std::vector<i64> sum(s.size() + 1, 0);

            i64 ans = 0, mod = 1e9 + 7;

            map[0] = 1;

            for (int i = 0; i < s.size(); i++) {
                sum[i + 1] = sum[i] + ((s[i] - '0') ? 1 : -1);
                ans += (s.size() - i) * map[sum[i + 1]] % mod;
                ans %= mod;
                map[sum[i + 1]] += i + 2;
                map[sum[i + 1]] %= mod;
            }

            std::cout << ans << '\n';
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