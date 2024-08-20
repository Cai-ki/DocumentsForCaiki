`Guess The Tree <https://codeforces.com/contest/2001/problem/C>`_
=====================================================================

    需要求的是一棵树，我们可以默认根节点为 ``1`` 号点。

    树上的任意一点的父亲节点唯一，因此我们可以直接求出所有非根节点的父亲节点，然后连边即可。

    求某个点的父亲节点很简单，设 ``x`` 初始值为 ``1`` ， ``y`` 初始值为当前要求的节点编号。

    我们可以一直询问 ``? x y`` ，假如返回的值不为 ``x`` 意味着 ``x`` 不是父亲，将 ``x`` 设为返回的值，一直重复以上操作，直到返回的值为 ``x`` ，此时父节点就为 ``x``。

    这样就能在 ``log`` 的复杂度下得到某个点的父节点。

    总的询问次数是 :math:`n\log n` 级别，题目所给的限制完全满足。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #define int long long

        void solve() {
            int n;
            std::cin >> n;
            std::vector<std::vector<int>> eg(n + 1);

            for (int i = 2; i <= n; i++) {
                int x = 1, y;
                std::cout << "? " << x << ' ' << i << std::endl;
                std::cin >> y;
                while (y != x) {
                    x = y;
                    std::cout << "? " << x << ' ' << i << std::endl;
                    std::cin >> y;
                }
                eg[x].push_back(i);
            }

            std::cout << '!';

            for (int i = 1; i <= n; i++) {
                for (auto it : eg[i]) {
                    std::cout << ' ' << i << ' ' << it;
                }
            }
            std::cout << std::endl;
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