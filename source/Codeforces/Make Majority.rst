`Make Majority <https://codeforces.com/contest/1988/problem/B>`_
=========================================================================

    为了构造出 ``[1]`` 数组，我们会很讨厌出现 ``0`` 。

    好消息是通过对连续的 ``0`` 进行操作，可以得到单独的 ``0`` 。

    我们统计这样情况下新数组的 ``01`` 个数。

    如果 ``1`` 个数更多，显然能构造出来。

    反之，我们可以知道，该数组必定无连续的 ``1`` ，并且两边必定存在至少 ``1`` 个 ``0``。

    这两种情况必定无解，因为任意操作不会让 ``1`` 比 ``0`` 的个数更多，只会让 ``1`` 不变或者变少。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;

            int cnt[2] = {0, 0}, f = -1;

            while (n--) {
                char x;
                std::cin >> x;
                x -= '0';
                if (x)
                    cnt[1]++;
                else if (x != f)
                    cnt[0]++;
                f = x;
            }

            std::cout << (cnt[1] > cnt[0] ? "Yes\n" : "No\n");
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
