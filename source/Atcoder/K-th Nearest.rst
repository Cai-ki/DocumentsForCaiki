`K-th Nearest <https://atcoder.jp/contests/abc364/tasks/abc364_d>`_
==========================================================================

    使用二分解决该问题。

    我们直接二分距离，在 ``check`` 函数中二分寻找距离询问的点小于指定距离的最左和最右侧位置。

    通过它们就可以计算出在这个距离以内有多少个坐标。

    我们会通过二分寻找最小的距离并且满足在这个距离范围的坐标数量要大于等于 ``k`` 个。

    这样就必定能够二分出正确答案，该算法时间复杂度为 :math:`q*log^{2}(n)`

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            int n, q;
            std::cin >> n >> q;
            std::vector<int> a(n);

            for (int i = 0; i < n; ++i) {
                std::cin >> a[i];
            }

            std::sort(a.begin(), a.end());

            while (q--) {
                int b, k;
                std::cin >> b >> k;

                auto check = [&](int u) -> bool {
                    int min = b - u, max = b + u;
                    int cnt = std::upper_bound(a.begin(), a.end(), max) -
                            std::lower_bound(a.begin(), a.end(), min);
                    return cnt >= k;
                };

                int l = 0, r = 1e9;
                while (l < r) {
                    int mid = (l + r) >> 1;
                    if (check(mid)) {
                        r = mid;
                    } else {
                        l = mid + 1;
                    }
                }
                std::cout << l << '\n';
            }

            return 0;
        }