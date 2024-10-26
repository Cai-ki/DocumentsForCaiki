Fenwick —— 树状数组
=====================

    .. code-block:: CPP

        template <typename T>
        struct Fenwick {
            int n;
            std::vector<T> tr;
            Fenwick(int n) : n(n), tr(n + 1) {}

            int lowbit(int x) { return x & -x; }

            void add(int i, T val) {
                for (; i <= n; i += lowbit(i)) {
                    tr[i] += val;
                }
            }

            T sum(T i) {
                T res = 0;
                for (; i > 0; i -= lowbit(i)) {
                    res += tr[i];
                }
                return res;
            }

            T rangeSum(int l, int r) {
                if (l > r) return 0;
                return sum(r) - sum(l - 1);
            }

            T kth(int k) {
                int x = 0;
                T res = 0;
                for (int i = 1 << std::__lg(n); i; i /= 2) {
                    if (x + i <= n && k >= tr[x + i]) {
                        x += i;
                        k -= tr[x];
                        res += tr[x];
                    }
                }
                return (k ? -1 : res);
            }
        };