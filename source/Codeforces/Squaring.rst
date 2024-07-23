`Squaring <https://codeforces.com/contest/1995/problem/C>`_
===============================================================

    该题的核心其实是比较 :math:`a^{2^{x}}` 和 :math:`b^{2^{y}}` 的大小， ``ab`` 为初始值， ``xy`` 为操作数。

    显然不能暴力求解。

    实际上我们可以只存储底数和操作数，通过这两个信息来比较两个数的大小。

    对两者取对数。

    :math:`a^{2^{x}}\ <\ b^{2^{y}}` 等价于 :math:`\ln_{}{a^{2^{x}}}\ <\ \ln_{}{b^{2^{y}}}`

    这样依旧不好算， 那就接着转化吧。

    :math:`\ln_{}{a^{2^{x}}}\ <\ \ln_{}{b^{2^{y}}}` 等价于 :math:`2^{x}*\ln_{}{a}\ <\ 2^{y}*\ln_{}{b}`

    :math:`2^{x}*\ln_{}{a}\ <\ 2^{y}*\ln_{}{b}` 等价于 :math:`\ln_{}{(2^{x}*\ln_{}{a})}\ <\ \ln_{}{(2^{y}*\ln_{}{b})}`

    :math:`\ln_{}{(2^{x}*\ln_{}{a})}\ <\ \ln_{}{(2^{y}*\ln_{}{b})}` 等价于 :math:`\ln_{}{2^{x}}+\ln_{}{(\ln_{}{a})}\ <\ \ln_{}{2^{y}}+\ln_{}{(\ln_{}{b})}`

    :math:`\ln_{}{2^{x}}+\ln_{}{(\ln_{}{a})}\ <\ \ln_{}{2^{y}}+\ln_{}{(\ln_{}{b})}` 等价于 :math:`x*\ln_{}{2}+\ln_{}{(\ln_{}{a})}\ <\ y*\ln_{}{2}+\ln_{}{(\ln_{}{b})}`

    :math:`x*\ln_{}{2}+\ln_{}{(\ln_{}{a})}\ <\ y*\ln_{}{2}+\ln_{}{(\ln_{}{b})}` 这个就是我们需要的最终的公式。

    得到这个式子后就简单多了，维护这两个信息，更新时就通过二分找到最小的操作数。

    注意精度问题！！！

    ``1.0 * u * log(2) + log(log(a[i])) >= 1.0 * fc * log(2) + log(log(a[i - 1])) - 0.00000001``

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            i64 ans = 0, fc = 0;

            for (int i = 1; i < n; i++) {
                int l = 0, r = 100000000;

                auto check = [&](int u) -> bool {
                    return 1.0 * u * log(2) + log(log(a[i])) >=
                        1.0 * fc * log(2) + log(log(a[i - 1])) - 0.00000001;
                };

                while (l < r) {
                    int mid = (l + r - 1) >> 1;
                    if (check(mid)) {
                        r = mid;
                    } else {
                        l = mid + 1;
                    }
                }

                if (!check(l)) {
                    std::cout << -1 << '\n';
                    return;
                }

                ans += (fc = l);
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