`Perform Operations to Maximize Score <https://codeforces.com/contest/1998/problem/C>`_
===========================================================================================

    由于 ``b`` 数组的存在可以发现删除 ``a`` 数组元素的对策并不相同。

    我们需要分类讨论。

    首先假如 :math:`b_i=1` 这时的最优决策是将 ``k`` 个操作全用到该位置。

    如果花费操作来将排序后前 :math:`\left \lfloor \frac{n}{2} \right \rfloor` 个元素中某个可增加的元素挪到后面，得到的新的中位数肯定小于等于挪到后面的这个元素。

    但原本的中位数加上操作数的贡献也肯定大于等于挪到后面的这个元素，因此花费操作挪其它元素是不赚的。

    因此，对于所有的 :math:`b_i=1` 的情况可以使用某些方法来算出删去这个位置所得到的中位数，就可以更新答案最大值。

    我这里用的是权值线段树动态开点来维护某个值域所存在的元素个数，然后通过二分找中位数（计算前会删除当前枚举到的数，之后会加上）。 

    接下来就是 :math:`b_i=0` 的情况。

    对于这种情况我们可以通过直接二分最大中位数来实现。

    在具体的 ``check`` 中我们会尽可能的使用操作，凑出更多的大于等于二分的中位数的数字（当然我们会先挑大的来操作，这样花费小）。

    之后则是挑不可更改的元素集合，显然如果能凑出 :math:`n-\left \lfloor \frac{n}{2} \right \rfloor` 个即以上意味着我们的中位数还能更大。

    而我们又可以发现当要求的中位数很小的时候我们的个数是很富裕的，因此我们可以挑出最大的不可更改的那个元素删除掉，依然不影响结果。

    由于答案是删除的数字的大小加上中位数，显然删除的越大越好。

    而在这个二分过程中，当中位数很小时我们其实删除的都是最大的那个。

    一直到恰好用完，我们才会选择挑完剩下的最大的那个删去。

    而再往后由于凑不出来，所以永远不会有更大的中位数。

    现在可以发现，在这个二分过程中实际上也就两种情况，富裕和不富裕。

    富裕情况肯定是选择中位数最大的那个情况。

    所以此时最优答案也就只有可能存在于这两者之一。

    我们二分的过程中算出来就行了。

    不过要注意，求解不富裕情况时可以通过只二分富裕情况，然后得到的中位数加一再 ``check`` 一下即可（如果把不富裕这种临界情况在二分过程判 ``true`` 会出错）。

    最后注意再 ``check`` 一下上下界即可。

    整体的时间复杂度为 :math:`O(n\log^2n + Cn\log n)`

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        constexpr int N = 2e5, MAX = 2e9;

        struct Node {
            int l, r, val;
        } tr[N * 40];

        int root, idx;

        void push_up(int u) { tr[u].val = tr[tr[u].l].val + tr[tr[u].r].val; }

        void modify(int &u, int L, int R, int x, int val) {
            if (!u) {
                u = ++idx;
            }
            if (L == R) {
                tr[u].val += val;
                return;
            }

            i64 mid = (1LL * L + R) >> 1;
            if (x <= mid) {
                modify(tr[u].l, L, mid, x, val);
            } else {
                modify(tr[u].r, mid + 1, R, x, val);
            }
            push_up(u);
        }

        int query(int u, int L, int R, int l, int r) {
            if (!u) {
                return 0;
            }
            if (l <= L && r >= R) {
                return tr[u].val;
            }

            int cnt = 0;
            i64 mid = (1LL * L + R) >> 1;

            if (l <= mid) {
                cnt += query(tr[u].l, L, mid, l, r);
            }
            if (r > mid) {
                cnt += query(tr[u].r, mid + 1, R, l, r);
            }
            return cnt;
        }

        void solve() {
            int n, k;
            std::cin >> n >> k;
            std::vector<int> a(n), b(n), x, y;

            for (auto &it : a) {
                std::cin >> it;
                modify(root, 1, MAX, it, 1);
            }

            for (auto &it : b) {
                std::cin >> it;
            }

            i64 ans = 0;
            int pos = n / 2;

            for (int i = 0; i < n; i++) {
                if (b[i]) {
                    modify(root, 1, MAX, a[i], -1);

                    int l = 1, r = MAX;
                    while (l < r) {
                        i64 mid = (1LL * l + r) >> 1;
                        if (query(root, 1, MAX, 1, mid) >= pos) {
                            r = mid;
                        } else {
                            l = mid + 1;
                        }
                    }

                    ans = std::max(ans, 1LL * a[i] + k + l);

                    modify(root, 1, MAX, a[i], 1);
                    y.push_back(a[i]);
                } else {
                    x.push_back(a[i]);
                }
            }

            auto check = [&](int u) -> bool {
                int K = k, cnt = n - pos;
                auto X = x, Y = y;

                while (Y.size() && Y.back() >= u) {
                    Y.pop_back();
                    cnt--;
                }

                while (Y.size() && u - Y.back() <= K) {
                    K -= u - Y.back();
                    Y.pop_back();
                    cnt--;
                }

                int max = X.back();
                while (X.size() && X.back() >= u) {
                    X.pop_back();
                    cnt--;
                }

                if (cnt < 0) {
                    ans = std::max(ans, 1LL * u + max);
                    return true;
                } else if (cnt == 0) {
                    ans = std::max(ans, 1LL * u + X.back());
                    return false;
                } else {
                    return false;
                }
            };

            if (x.size()) {
                std::sort(x.begin(), x.end());
                std::sort(y.begin(), y.end());

                int l = 1, r = MAX;
                while (l < r) {
                    i64 mid = (1LL * l + r + 1) >> 1;
                    if (check(mid)) {
                        l = mid;
                    } else {
                        r = mid - 1;
                    }
                }
                check(1);
                check(MAX);
                if (l + 1 <= MAX) check(l + 1);
            }

            std::cout << ans << '\n';

            for (int i = 0; i <= idx; i++) {
                tr[i] = {};
            }
            root = idx = 0;
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