.. index:: 可持久化字符串

Rope
=====

``Rope`` 是 ``C++ STL`` 中 ``pb_ds(Policy-Based Data Structures)`` 库的一个分支，采用可持久化平衡树实现。

在一些需要可持久化数据结构的题目中，可以用于代替实现可持久化平衡树、可持久化线段树（主席树）、可持久化并查集等等。

其插入，删除，访问的时间复杂度都是 ``O(log(n))`` ，拷贝的时间复杂度是 ``O(1)``。

但遗憾的是， ``Rope`` 的初衷是作为超级 ``string`` 来使用的，尽管底层是可持久化平衡树实现的，但它的功能是被割腌的。

不过，可持久的特性倒是保留了下来，由于 ``Rope`` 实例拷贝的时间复杂度是 ``O(1)`` ，我们就可以通过保存每个版本来实现可持久化。

``Rope`` 实现持久化可以参考 ``Python`` 的引用计数机制。

当拷贝一个 ``Rope`` 实例时，如果没有更改数据，实际上不会进行真实的数据复制。只是增加了最初实例所管理数据的引用计数。

在进行更改操作时，仅修改需要更改的部分节点。

尽管功能十分强大， ``Rope`` 还是有很大的局限性。

性能，占用空间都是它的劣势。

最重要的一点是，为了实现某些功能往往要比正解的数据结构多带一个 ``log`` 量级的复杂度，而且有可能会遇到卡常的情况。

这种情况也不绝对，当使用 ``Rope`` 进行可持久化时，其它数据结构操作的时间复杂度能达到 ``log * log`` ，而 ``Rope`` 理论上则依然是 ``log`` ，这都是由于 ``O(1)`` 拷贝所带来的优势，这也使得在可持久化方面 ``Rope`` 勉强能和这些数据结构 ``46`` 开。

当然，这些劣势在 ``Rope`` 极其简单的使用方式下都不足挂齿，它真的是太好写了。

接下来讲一讲怎么使用 ``Rope`` 。

- ``Rope`` 的引用很方便， ``#include <ext/rope>`` 或者 ``#include<bits/extc++.h>`` 皆可。它的命名空间是 ``__gnu_cxx`` 。
- ``Rope`` 的声明可以参照 ``vector`` ，特殊的， ``__gnu_cxx::rope<char>`` 等价于 ``__gnu_cxx::crope`` 。
    
    .. code-block:: CPP

        __gnu_cxx::rope<int> rope;
        __gnu_cxx::rope<char> crope;    

- ``Rope`` 的使用可以参照 ``vector`` 与 ``string`` ，常用的成员函数包括：
    
    - ``push_back(x)``
    - ``insert(pos,x)``
    - ``substr(pos,len)``
    - ``erase(pos,len)``
    - ``begin()``
    - ``end()``
    - ``size()``
    - ``empty()``
    - ``clear()``

  这些大家都很熟悉了，具体用法也是你想的那样，这里不多做解释。当然了，还有很多其它的成员函数没有包括，可自行搜索。
  
  对了， ``Rope`` 可以正常的使用 ``std::cin`` 和 ``std::cout`` ， 你也可以像 ``string`` 那样用 ``+`` 号拼接两个 ``Rope`` 实例。


接下来就写点例题巩固一下吧。

`P3369 【模板】普通平衡树 <https://www.luogu.com.cn/problem/P3369>`_
************************************************************************

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #include <ext/rope>

        int main() {
            int n, inf = 1e9;
            std::cin >> n;
            __gnu_cxx::rope<int> rope;
            rope.push_back(-inf);
            rope.push_back(inf);

            auto lower_bound = [&](int u) -> int {
                return std::lower_bound(rope.begin(), rope.end(), u) - rope.begin();
            };

            auto upper_bound = [&](int u) -> int {
                return std::upper_bound(rope.begin(), rope.end(), u) - rope.begin();
            };

            while (n--) {
                int op, x;
                std::cin >> op >> x;

                if (op == 1) {
                    int idx = lower_bound(x);
                    rope.insert(idx, x);
                } else if (op == 2) {
                    int idx = lower_bound(x);
                    rope.erase(idx, 1);
                } else if (op == 3) {
                    int idx = lower_bound(x);
                    std::cout << idx << '\n';
                } else if (op == 4) {
                    std::cout << rope[x] << '\n';
                } else if (op == 5) {
                    int idx = lower_bound(x);
                    std::cout << rope[idx - 1] << '\n';
                } else {
                    int idx = upper_bound(x);
                    std::cout << rope[idx] << '\n';
                }
            }

            return 0;
        }

`P3391 【模板】文艺平衡树 <https://www.luogu.com.cn/problem/P3391>`_
**********************************************************************

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #include <ext/rope>

        int main() {
            int n, m;
            std::cin >> n >> m;
            __gnu_cxx::rope<int> a, b;

            for (int i = 0; i < n; i++) {
                a.append(i + 1);
                b.append(n - i);
            }

            while (m--) {
                int l, r;
                std::cin >> l >> r;
                --l, --r;
                int len = r - l + 1;

                auto center = a.substr(l, len);
                a = a.substr(0, l) + b.substr(n - l - len, len) +
                    a.substr(r + 1, n - (r + 1));
                b = b.substr(0, n - l - len) + center + b.substr(n - l, n - (n - l));
            }

            for (auto it : a) {
                std::cout << it << ' ';
            }

            return 0;
        }

`P6136 【模板】普通平衡树（数据加强版） <https://www.luogu.com.cn/problem/P6136>`_
************************************************************************************
    
    这道题使用 ``Rope`` 就通过不了。

    ``23`` 个点只过了 ``9`` 个。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #include <ext/rope>

        int main() {
            int n, m, inf = (1 << 30);
            std::cin >> n >> m;

            std::vector<int> a(n);

            for (auto &it : a) {
                std::cin >> it;
            }

            std::sort(a.begin(), a.end());

            __gnu_cxx::rope<int> t;
            t.push_back(-inf);
            for (auto it : a) {
                t.push_back(it);
            }
            t.push_back(inf);

            int res = 0, last = 0;

            auto find = [&](int u) -> int {
                return std::lower_bound(t.begin(), t.end(), u) - t.begin();
            };

            auto find_next = [&](int u) -> int {
                return std::upper_bound(t.begin(), t.end(), u) - t.begin();
            };

            while (m--) {
                int op, x;
                std::cin >> op >> x;

                x ^= last;

                if (op == 1) {
                    int idx = find(x);
                    t.insert(idx, x);
                } else if (op == 2) {
                    int idx = find(x);
                    t.erase(idx, 1);
                } else if (op == 3) {
                    int idx = find(x);
                    last = idx;
                    res ^= idx;
                } else if (op == 4) {
                    last = t[x];
                    res ^= t[x];
                } else if (op == 5) {
                    int idx = find(x);
                    last = t[idx - 1];
                    res ^= t[idx - 1];
                } else {
                    int idx = find_next(x);
                    last = t[idx];
                    res ^= t[idx];
                }
            }

            std::cout << res << '\n';

            return 0;
        }

`P3835 【模板】可持久化平衡树 <https://www.luogu.com.cn/problem/P3835>`_
***********************************************************************************

    这题 ``25`` 个点， ``TLE`` 了两个点。可以看到，和正常写法相比还是过于取巧了。

    不过至少过了大部分测试数据不是吗？

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #include <ext/rope>

        int main() {
            int n, inf = 2147483647;
            std::cin >> n;
            __gnu_cxx::rope<int> rope;
            std::vector<__gnu_cxx::rope<int>> backup;
            rope.push_back(-inf);
            rope.push_back(inf);
            backup.push_back(rope);

            auto lower_bound = [&](int u) -> int {
                return std::lower_bound(rope.begin(), rope.end(), u) - rope.begin();
            };

            auto upper_bound = [&](int u) -> int {
                return std::upper_bound(rope.begin(), rope.end(), u) - rope.begin();
            };

            while (n--) {
                int v, op, x;
                std::cin >> v >> op >> x;

                rope = backup[v];

                if (op == 1) {
                    int idx = lower_bound(x);
                    rope.insert(idx, x);
                } else if (op == 2) {
                    int idx = lower_bound(x);
                    if (x == rope[idx]) rope.erase(idx, 1);
                } else if (op == 3) {
                    int idx = lower_bound(x);
                    std::cout << idx << '\n';
                } else if (op == 4) {
                    std::cout << rope[x] << '\n';
                } else if (op == 5) {
                    int idx = lower_bound(x);
                    std::cout << rope[idx - 1] << '\n';
                } else {
                    int idx = upper_bound(x);
                    std::cout << rope[idx] << '\n';
                }
                backup.push_back(rope);
            }

            return 0;
        }

`P3919 【模板】可持久化线段树 1（可持久化数组） <https://www.luogu.com.cn/problem/P3919>`_
****************************************************************************************************

    ``12`` 个点 ``TLE`` 了 ``4`` 个。

    测试数据太狠了。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        #include <ext/rope>

        int main() {
            int n, m;
            std::cin >> n >> m;
            __gnu_cxx::rope<int> rope;
            std::vector<__gnu_cxx::rope<int>> backup;

            while (n--) {
                int x;
                std::cin >> x;
                rope.push_back(x);
            }

            backup.push_back(rope);

            while (m--) {
                int v, op, loc, x;
                std::cin >> v >> op >> loc;
                --loc;
                rope = backup[v];

                if (op == 1) {
                    std::cin >> x;
                    rope.replace(loc, x);
                } else {
                    std::cout << rope[loc] << '\n';
                }

                backup.push_back(rope);
            }

            return 0;
        }
