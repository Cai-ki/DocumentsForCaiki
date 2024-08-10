`Cuboid Sum Query <https://atcoder.jp/contests/abc366/tasks/abc366_d>`_
================================================================================

    看时间限制发现用不上三维前缀和，就做个二维前缀和就行了，余下的一维暴力求和就行。

    很典。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int N;
            std::cin >> N;
            std::vector<std::vector<std::vector<i64>>> a(
                N + 1,
                std::vector<std::vector<i64>>(N + 1, std::vector<i64>(N + 1, 0)));

            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    for (int k = 1; k <= N; k++) {
                        std::cin >> a[i][j][k];
                    }
                }
            }

            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    for (int k = 1; k <= N; k++) {
                        a[i][j][k] = a[i][j - 1][k] + a[i][j][k - 1] -
                                    a[i][j - 1][k - 1] + a[i][j][k];
                    }
                }
            }

            int q;
            std::cin >> q;

            while (q--) {
                int l[4], r[4];
                std::cin >> l[1] >> r[1] >> l[2] >> r[2] >> l[3] >> r[3];

                i64 sum = 0;
                for (int i = l[1]; i <= r[1]; i++) {
                    sum += a[i][r[2]][r[3]] + a[i][l[2] - 1][l[3] - 1] -
                        a[i][l[2] - 1][r[3]] - a[i][r[2]][l[3] - 1];
                }

                std::cout << sum << '\n';
            }

            return 0;
        }