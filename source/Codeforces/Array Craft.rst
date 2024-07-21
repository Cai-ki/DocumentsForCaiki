`Array Craft <https://codeforces.com/contest/1990/problem/B>`_
===============================================================

    在数据范围中， ``x`` 不等于 ``y`` ，将 ``y`` 到 ``x`` 这一段区间全都设置为 ``1`` ，总和必定大于等于 ``2`` 。

    如果能够有一种构造方法，让这段区间的前缀和后缀不出现正向贡献与此同时负向贡献也不能过大，此时就能构造出正确答案。

    由于可设置的数字只能是 ``1`` 或者 ``-1`` ， 假如构造两者交替出现的排列，我们能够得到两种贡献类型的排列。

    一种是 ``1`` ， ``0`` 交替出现，另一种是 ``-1`` ， ``0`` 交替出现。

    若需要构造前缀或后缀，应在与中间区间相邻的位置设置为 ``-1`` 并以此为基础构造交替排列。

    可以证明，无论前缀或者后缀长度为奇数还是偶数，按这样构造都是正确的。

    对四种情况讨论即可，过于简单，这里不做具体证明。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, x, y;
            std::cin >> n >> x >> y;

            for (int i = 0, j = ((y - 1) & 1 ? -1 : 1); i < y - 1; i++, j *= -1) {
                std::cout << j << ' ';
            }
            for (int i = y - 1; i < x; i++) {
                std::cout << 1 << ' ';
            }
            for (int i = x, j = -1; i < n; i++, j *= -1) {
                std::cout << j << ' ';
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