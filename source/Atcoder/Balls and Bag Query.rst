`Balls and Bag Query <https://atcoder.jp/contests/abc366/tasks/abc366_c>`_
===============================================================================

    开个桶维护一下就行了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int q;
            std::cin >> q;

            std::vector<int> a(1e6 + 1, 0);
            int cnt = 0;

            while (q--) {
                int op, x;
                std::cin >> op;
                if (op == 1) {
                    std::cin >> x;
                    if (!(a[x]++)) {
                        cnt++;
                    }
                } else if (op == 2) {
                    std::cin >> x;
                    if (!(--a[x])) {
                        cnt--;
                    }
                } else {
                    std::cout << cnt << '\n';
                }
            }

            return 0;
        }