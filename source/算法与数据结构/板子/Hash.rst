Hash —— 哈希
==============

    .. code-block:: CPP

        template <int b1 = 131, int b2 = 1331, int m1 = 998244353,
                int m2 = (int)(1e9 + 7)>
        struct Hash {
            int len;
            std::string s;
            std::vector<int> h1, p1, h2, p2;

        public:
            Hash(std::string &s)
                : len(s.size()),
                s(s),
                h1(len + 1),
                p1(len + 1),
                h2(len + 1),
                p2(len + 1) {
                p1[0] = p2[0] = 1;

                for (int i = 0; i < len; i++) {
                    p1[i + 1] = p1[i] * b1 % m1;
                    p2[i + 1] = p2[i] * b2 % m2;

                    h1[i + 1] = (h1[i] * b1 % m1 + s[i] - 'a') % m1;
                    h2[i + 1] = (h2[i] * b2 % m2 + s[i] - 'a') % m2;
                }
            }

            std::pair<int, int> get_hash(int l, int r) {
                int res1 = ((h1[r] - h1[l - 1] * p1[r - l + 1] % m1) % m1 + m1) % m1;
                int res2 = ((h2[r] - h2[l - 1] * p2[r - l + 1] % m2) % m2 + m2) % m2;
                return {res1, res2};
            }
        };