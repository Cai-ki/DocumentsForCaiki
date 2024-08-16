`Not a Nim Problem <https://codeforces.com/contest/2004/problem/E>`_
============================================================================

    打表发现所有数的 ``sg`` 函数的值是其最小质因数 ``sg`` 函数的值。

    筛一遍然后赋值异或即可。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        constexpr int N = 1e7;

        int sg[N + 1], minp[N + 1];
        std::vector<int> primes;

        void init() {
            for (int i = 2; i <= N; i++) {
                if (minp[i] == 0) {
                    primes.push_back(i);
                    minp[i] = i;
                }

                for (auto p : primes) {
                    if (i * p > N) {
                        break;
                    }
                    minp[i * p] = p;
                    if (p == minp[i]) {
                        break;
                    }
                }
            }

            sg[0] = 0;
            sg[1] = 1;
            sg[2] = 0;
            for (int i = 1; i < primes.size(); i++) {
                sg[primes[i]] = i + 1;
            }
            for (int i = 3; i <= N; i++) {
                sg[i] = sg[minp[i]];
            }
        }

        void solve() {
            int n;
            std::cin >> n;
            std::vector<int> a(n);

            int res = 0;

            for (auto &it : a) {
                std::cin >> it;

                res ^= sg[it];
            }

            if (res) {
                std::cout << "Alice\n";
            } else {
                std::cout << "Bob\n";
            }
        }

        signed main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            init();

            int t;
            std::cin >> t;

            while (t--) {
                solve();
            }

            return 0;
        }