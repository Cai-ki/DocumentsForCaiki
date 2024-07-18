`Fun Game <https://codeforces.com/contest/1994/problem/B>`_
===============================================================

    可以发现，对于序列 ``s`` 上的某个位置，如果需要对其进行更改只能通过其前方（包括自身）的元素对该元素进行影响。

    对于一种特殊情况， ``s`` 的首位为 ``1`` ，我们必定可以通过异或首位来构造出 ``t`` （最后首位可以通过异或自身来更改成 ``0`` ）。

    因此发现， ``s`` 中的 ``1`` 很重要，越靠前越好。

    接下来讨论 ``s`` 最先出现为 ``0`` 的情况，假如说对应位置的 ``t`` 也为 ``0`` ，这个位置显然可以忽视掉。

    若为 ``1`` ，很显然无药可救，此时 ``s`` 的前缀并不能为该位置提供 ``1`` 。

    综上看来， ``00`` 最先出现对答案无影响 , ``11`` 和 ``10`` 最先出现代表必定能构造出 ``t`` ， ``01`` 最先出现则代表构造不出 ``t`` 。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n;
            std::cin >> n;

            std::string s, t;
            std::cin >> s >> t;

            bool ok = true;
            for (int i = 0; i < n; i++) {
                if (s[i] == '1') {
                    break;
                }
                if (t[i] == '1') {
                    ok = false;
                    break;
                }
            }
            if (ok) {
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