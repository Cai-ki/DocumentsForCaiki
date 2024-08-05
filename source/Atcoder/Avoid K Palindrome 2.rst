`Avoid K Palindrome 2 <https://atcoder.jp/contests/abc363/tasks/abc363_c>`_
====================================================================================

    使用 ``std::next_permutation()`` 得到字符串的所有排列，然后暴力判断即可。

    ``std::next_permutation()`` 是得到字典序比当前排列大的所有排列中字典序最小的那个排列。

    ``std::prev_permutation()`` ， ``std::next_permutation()`` 。求前一个和后一个排列。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int N, K;
            std::string S;

            std::cin >> N >> K >> S;

            std::sort(S.begin(), S.end());

            int ans = 0;

            do {
                bool ok = true;
                for (int i = 0; i <= N - K; i++) {
                    int cnt = 0;
                    for (int j = 0; j < K / 2; j++) {
                        cnt += (S[i + j] != S[i + K - j - 1]);
                    }

                    if (!cnt) {
                        ok = false;
                        break;
                    }
                }
                if (ok) {
                    ans++;
                }

            } while (std::next_permutation(S.begin(), S.end()));

            std::cout << ans << '\n';

            return 0;
        }