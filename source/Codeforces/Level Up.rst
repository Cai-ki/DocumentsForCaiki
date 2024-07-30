`Level Up <https://codeforces.com/contest/1997/problem/E>`_
================================================================

    思路就是找一个 ``k`` 的分界点，小于等于这个值的就暴力。

    大于这个值的先维护一系列前缀和数组，这些前缀和数组是用来求区间大于某个值的元素数量。

    可以发现，当 ``k`` 值足够大时，等级其实不会太高。

    前缀和数组的数量其实就是等级最大值（随便找个大于并接近的值就行）。

    完全可以暴力加二分处理剩下 ``k`` 大于分界点的情况。

    枚举每个等级，通过二分找到打完刚好升级的那个位置，然后跳到它后面，一直持续到找到能包含询问元素的区间。

    然后就根据此时等级算答案就行。

    整体思路大概就是这样，实在是不想细说下去，这场太唐了，没啥心情，大概了解思路意思意思就得了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int n, m;
            std::cin >> n >> m;

            std::vector<int> a(n + 1);

            for (int i = 1; i <= n; i++) {
                std::cin >> a[i];
            }

            std::vector<std::vector<int>> cnt(n + 1, std::vector<int>(120, 0));

            for (int i = 1; i < 120; i++) {
                for (int j = 1; j <= n; j++) {
                    cnt[j][i] = cnt[j - 1][i] + (a[j] >= i);
                }
            }

            std::vector<bool> ans(m);
            std::vector<std::pair<int, int>> query[2001];

            for (int i = 0; i < m; i++) {
                int x, k;
                std::cin >> x >> k;
                if (k <= 2000) {
                    query[k].push_back({x, i});
                } else {
                    int l, r, L = 1;

                    for (int g = 1; g < 120; g++) {
                        l = L, r = n;
                        while (l < r) {
                            int mid = (l + r) >> 1;
                            if (cnt[mid][g] - cnt[L - 1][g] >= k) {
                                r = mid;
                            } else {
                                l = mid + 1;
                            }
                        }
                        if (x >= L && x <= l) {
                            ans[i] = (a[x] >= g);
                            break;
                        }

                        if (cnt[l][g] - cnt[L - 1][g] >= k) {
                            L = l + 1;
                        } else {
                            break;
                        }
                    }
                }
            }

            for (int i = 1; i <= 2000; i++) {
                if (query[i].empty()) continue;

                std::sort(query[i].begin(), query[i].end());

                std::vector<bool> result(query[i].back().first + 1);

                int g = 1, c = 0;
                for (int j = 1; j <= query[i].back().first; j++) {
                    if (g > a[j]) {
                        result[j] = false;
                    } else {
                        result[j] = true;
                        c++;
                        if (c >= i) {
                            c -= i;
                            g++;
                        }
                    }
                }

                for (auto [x, q] : query[i]) {
                    ans[q] = result[x];
                }
            }
            for (auto it : ans) {
                std::cout << (it ? "YES\n" : "NO\n");
            }

            return 0;
        }