`D-Function <https://codeforces.com/contest/1985/problem/G>`_
==============================================================

    通过打表可以发现一个规律，假如一个数字满足每一位乘上 ``k`` 不会进位，那么这个数字也就满足题目给出的等式。

    那么就是一个简单的组合数学问题了，对于一个给定的 ``x`` ，想求出满足 :math:`a<10^x` ，的满足上方等式的数字的个数，只需要在每一位任意填小于 ``9 / k + 1`` 的数字即可。

    每一位都有这么多方案，那么整合起来就应该是 :math:`(\left \lfloor \frac{9}{k} \right \rfloor  + 1)^x` 个合法方案。

    ``x`` 范围很大，这里可以用快速幂来计算。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int l, r, k, mod = 1e9 + 7;
            std::cin >> l >> r >> k;

            auto get = [&](int u) -> i64 {
                i64 res = 1, p = 9 / k + 1;

                while (u > 0) {
                    if (u & 1) res = (res * p) % mod;
                    p = (p * p) % mod;
                    u >>= 1;
                }

                return res;
            };

            std::cout << (get(r) - get(l) + mod) % mod << '\n';
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