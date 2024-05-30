Trails (Medium)
==================

`题目链接 <https://codeforces.com/problemset/problem/1970/E2>`_

.. code-block:: text
    :caption: 思路

    1.首先我们继承Trails (Easy)的整体思路。
    2.我们发现n的范围很大，同时观察Trails (Easy)的更新状态过程。
    3.可以考虑用矩阵快速幂，对于每一天的(矩阵)dp，我们都可以由(矩阵)dp*cnt(前一天)得到，化简得dp = dp*((cnt)^n)。
    4.这里的dp是一个二维数组，和Trails (Easy)中的不同。第一维大小为1，第二维大小为m。这是为了方便矩阵乘法。
    5.最后求和即可。

.. code-block:: cpp
    :caption: 代码

    #include <bits/stdc++.h>
    #define all(a) a.begin(), a.end()
    #define int long long
    #define PII pair<int, int>
    using ll = long long;
    const int N = 1e5 + 10, INF = 0x3f3f3f3fll, MOD = 1e9 + 7;
    using namespace std;

    class Mat
    {
        int rows, columns, _MOD = 1e9 + 7;
        vector<vector<int>> matrix;

    public:
        Mat(int rows, int columns) : rows(rows), columns(columns) { matrix = vector<vector<int>>(rows, vector<int>(columns, 0)); }
        Mat(vector<vector<int>> matrix) : matrix(matrix) { rows = matrix.size(), columns = matrix[0].size(); }
        Mat get_Z() { return Mat(rows, columns); }
        Mat get_E()
        {
            Mat Z = get_Z();
            for (int i = 0; i < rows; i++)
                Z[i][i] = 1;
            return Z;
        }
        vector<int> &operator[](int index) { return matrix[index]; }
        const vector<int> &operator[](int index) const { return matrix[index]; }
        Mat operator*(const Mat &other)
        {
            int _rows = rows, _columns = other.columns;
            Mat _matrix(_rows, _columns);
            for (int i = 0; i < _rows; i++)
                for (int j = 0; j < _columns; j++)
                    for (int k = 0; k < columns; k++)
                        _matrix[i][j] = (_matrix[i][j] + matrix[i][k] * other[k][j] % _MOD) % _MOD;
            return _matrix;
        }
        Mat pow(int n)
        {
            Mat result = get_E(), base = *this;
            while (n)
            {
                if (n & 1)
                    result = result * base;
                base = base * base;
                n >>= 1;
            }
            return result;
        }
    };

    void solve()
    {
        int m, n;
        cin >> m >> n;

        vector<int> s(m), l(m);

        for (int i = 0; i < m; i++)
            cin >> s[i];
        for (int i = 0; i < m; i++)
            cin >> l[i];

        Mat dp(1, m), cnt(m, m);
        dp[0][0] = 1;

        for (int i = 0; i < m; i++)
            for (int j = 0; j < m; j++)
                cnt[i][j] = cnt[j][i] = (s[i] * s[j] + s[i] * l[j] + l[i] * s[j]) % MOD;

        dp = dp * cnt.pow(n);

        cout << accumulate(all(dp[0]), 0ll) % MOD << '\n';
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