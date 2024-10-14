`Link Summon <https://codeforces.com/gym/105222/problem/B>`_
==============================================================

    贪心 ``+`` 分类讨论。

    尽可能先让两两结合，之后三三， ``etc`` 。 

    .. code-block::CPP

        #ifndef CAIKI_LOCAL
        #include <bits/stdc++.h>
        #endif

        #ifdef CAIKI_LOCAL
        #include <algorithm>
        #include <iostream>
        #include <vector>

        auto _ = []() {
        freopen("./io/in.txt", "r", stdin);
        // freopen("./io/out.txt", "w", stdout);
        return true;
        }();
        #endif

        #define int long long

        void solve() {
        std::vector<int> a(6);
        for (int i = 1; i <= 5; i++) {
            std::cin >> a[i];
        }

        int ans = 0;

        int del = std::min(a[1], a[5]);
        ans += del;
        a[1] -= del, a[5] -= del;

        del = std::min(a[2], a[4]);
        ans += del;
        a[2] -= del, a[4] -= del;

        ans += a[3] / 2;
        a[3] = a[3] & 1;

        for (int i = 5; i >= 1; i--) {
            if (!a[i]) {
            continue;
            }
            if (i == 5) {
            for (int j = 2; j <= 4; j++) {
                del = std::min(a[j], a[5]);
                ans += del;
                a[j] -= del, a[5] -= del;
            }
            ans += a[5] / 2;
            } else if (i == 4) {
            int f = 0;
            for (int j = 1; j <= 3; j++) {
                del = std::min(a[j] / 2, a[4]);
                ans += del;
                a[j] -= del * 2, a[4] -= del;
                if (a[j]) {
                f++;
                }
            }
            if (a[4] >= 1 && f == 2) {
                ans++;
                a[1]--, a[3]--, a[4]--;
            }
            if (a[4] >= 2 && f == 1) {
                ans++;
                a[4] -= 2;
            }
            ans += a[4] / 3;
            } else if (i == 3) {
            if (a[1] && a[2]) {
                ans++;
                a[1]--, a[2]--, a[3] = 0;
            } else if (a[1]) {
                del = a[1] >= 3;
                ans += del;
                a[1] -= del * 3;
            } else if (a[2]) {
                del = a[2] >= 2;
                ans += del;
                a[2] -= del * 2;
            }
            } else if (i == 2) {
            ans += a[2] / 3, a[2] %= 3;
            if (a[2] == 0) {
                continue;
            }
            del = a[1] >= (a[2] == 1 ? 4 : 2);
            ans += del;
            a[1] -= del * (a[2] == 1 ? 4 : 2);
            } else if (i == 1) {
            ans += a[1] / 6;
            }
        }

        std::cout << ans << '\n';
        }

        signed main() {
        std::ios::sync_with_stdio(false);
        std::cin.tie(nullptr);

        int t;
        std::cin >> t;

        while (t--) {
            solve();
        }

        return 0;
        }