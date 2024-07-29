`AND Reconstruction <https://codeforces.com/contest/1991/problem/B>`_
=======================================================================

    根据题目 :math:`b_{i}=a_{i}\&a_{i+1}` 。

    发现 :math:`a_{i}=b_{i-1}|b_{i}` ，应该说 :math:`a_{i}` 至少得满足这些二进制位为 ``1`` 的位置上为 ``1``。

    如无必要勿增实体，所以可以认为 :math:`a_{i}=b_{i-1}|b_{i}` 。

    因此可以先构造出 ``a`` 数组，然后判断是否满足 ``b`` 数组即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n), b(n);

            for (int i = 0; i < n - 1; i++) {
                std::cin >> b[i];
            }

            a[0] = b[0];

            for (int i = 1; i < n; i++) {
                a[i] = b[i - 1] | b[i];
            }

            for (int i = 0; i < n - 1; i++) {
                if ((a[i] & a[i + 1]) != b[i]) {
                    std::cout << -1 << '\n';
                    return;
                }
            }
            for (int i = 0; i < n; i++) {
                std::cout << a[i] << ' ';
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