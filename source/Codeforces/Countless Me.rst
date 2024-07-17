`Countless Me <https://codeforces.com/gym/105143/problem/B>`_
================================================================

    我们可以在n次操作内把数组内所有值改成任意值(sum不变)。
    
    可以考虑贪心，为了让答案尽可能小，我们从最高为开始贪。
    
    如果后面所有位都存不下当前sum(意味着((1 << (bit)) - 1) * n < sum)，我们将会放入当前位，否则放到低位更优。
    
    当前位最多放置n次，即这一步最多会让sum减去(n * (1 << (bit)))，我们求出具体减去的个数，更新sum即可。
    
    特别注意不能从太高位开始贪，会爆longlong。
    
    注意(1 << bit)会爆longlong的问题，应改成(1ll << (bit)).

    .. code-block:: cpp

        #include <bits/stdc++.h>
        #define all(a) a.begin(), a.end()
        #define int long long
        #define PII pair<int, int>
        using ll = long long;
        const int N = 1e5 + 10, INF = 1e18, MOD = 1e9 + 7;
        using namespace std;

        void solve()
        {
            int n, sum = 0;
            cin >> n;

            vector<int> a(n);

            for (int i = 0; i < n; i++)
            {
                cin >> a[i];
                sum += a[i];
            }

            int ans = 0;

            for (int i = 40; i >= 0; i--)
            {

                ans <<= 1;

                if (sum <= 0 || ((1ll << i) - 1ll) * n >= sum)
                    continue;

                int cnt = min(n, sum / (1ll << i));

                sum -= cnt * (1ll << i);
                ans++;
            }

            cout << ans << "\n";
        }
        signed main()
        {
            ios::sync_with_stdio(0), cin.tie(0);
            int T = 1;
            // cin >> T, cin.get();
            while (T--)
                solve();
            return 0;
        }