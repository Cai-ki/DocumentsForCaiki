`Beauty of the mountains <https://codeforces.com/contest/1982/problem/D>`_
===================================================================================

    对于任何一个 ``k * k`` 的块，我们得到了两种山脉的数量差 ``del`` ，假如两种山脉高度和的差为 ``sum`` ，如果 ``sum`` 能用 ``del`` 表示，意味着有解。

    当然，我们有很多 ``k * k`` 的块，如果有解，必定是选择这些块中的某些进行增添。

    这里需要用到数学知识：裴蜀定理。

    :math:`\forall a,b \in Z , \exists x,y  \in Z,ax+by= gcd(a,b)`

    一些简单推论：

    - :math:`gcd(a_{1},a_{2},...,a_{n}) = gcd(gcd(a_{1},a_{2},...,a_{n-1}),a_{n})`

    - :math:`\forall a_{1},a_{2},...,a_{n} \in Z , \exists x_{1},x_{2},...,x_{n}  \in Z,a_{1}x_{1}+a_{2}x_{2}+...+a_{n}x_{n}= gcd(a_{1},a_{2},...,a_{n})`

    借助以上推论，解题思路就显而易见了。

    只需要求所有 ``k * k`` 的块的两种山脉的数量差的 ``gcd`` 就可以了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n, m, k;
            std::cin >> n >> m >> k;

            std::vector<std::vector<int>> a(n, std::vector<int>(m));
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < m; ++j) {
                    std::cin >> a[i][j];
                }
            }

            std::vector<std::vector<int>> b(n + 1, std::vector<int>(m + 1, 0));

            i64 sum = 0;

            for (int i = 0; i < n; i++) {
                std::string s;
                std::cin >> s;

                for (int j = 0; j < m; j++) {
                    b[i + 1][j + 1] = s[j] == '0' ? 1 : -1;
                    sum += b[i + 1][j + 1] * a[i][j];
                    b[i + 1][j + 1] += b[i + 1][j];
                }
            }

            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= m; j++) {
                    b[i][j] += b[i - 1][j];
                }
            }

            int gcd = 0;

            for (int i = 0; i <= n - k; i++) {
                for (int j = 0; j <= m - k; j++) {
                    int del = b[i][j] - b[i][j + k] - b[i + k][j] + b[i + k][j + k];
                    gcd = std::gcd(gcd, abs(del));
                }
            }

            if (sum == 0 || (gcd && sum % gcd == 0)) {
                std::cout << "YES\n";
            } else {
                std::cout << "NO\n";
            }
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