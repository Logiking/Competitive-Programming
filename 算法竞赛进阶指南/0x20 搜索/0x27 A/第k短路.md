### $A*$算法
___
先建反向边，跑一遍$Dijkstra$，并作估价函数，然后再正向跑一遍$Dijkstra$，使用真实距离和估价函数进行排序，当一个点出队不超过$k$次时，更新
___
````c++
#include <bits/stdc++.h>

using namespace std;

using i64 = long long;
using ull = unsigned long long;
using pii = pair<int, int>;
using piii = pair<int, pii>;

struct Dijkstra {
    int n;
    const int inf;

    std::vector<std::vector<std::pair<int, int>>> g;
    std::vector<int> d;

    Dijkstra(int n, const int& inf) : n(n), inf(inf), g(n) {}

    void add(int a, int b, int c) {
        g[a].emplace_back(b, c);
    }

    int run(int s, int t) {
        d.resize(n);
        d.assign(n, inf);
        std::priority_queue<pair<int, int>, std::vector<pair<int, int>>, greater<pair<int, int>>> q;
        d[s] = 0;
        q.emplace(d[s], s);
        while (q.size()) {
            auto t = q.top();
            q.pop();
            if (t.first != d[t.second])
                continue;
            for (auto tem : g[t.second]) {
                int dis = t.first + tem.second;
                if (dis < d[tem.first]) {
                    d[tem.first] = dis;
                    q.emplace(dis, tem.first);
                }
            }
        }

        return d[t] == inf ? -1 : d[t];
    }
};

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);

    int n, m, S, T, K;
    cin >> n >> m;

    vector<vector<pair<int, int>>> g(n + 1);

    Dijkstra rg(n + 1, 0x3f3f3f3f);
    for (int i = 0; i < m; i ++) {
        int a, b, c;
        cin >> a >> b >> c;
        g[a].push_back({b, c});
        rg.add(b, a , c);
    }

    cin >> S >> T >> K;
    rg.run(T, S);

    if (S == T)
        K ++;

    vector<int> vis(n + 1);

    auto a_star = [&] () -> int {
        priority_queue<piii, vector<piii>, greater<piii>> heap;
        heap.push({rg.d[T], {0, S}});

        while (heap.size()) {
            auto now = heap.top();
            heap.pop();

            int ver = now.second.second, dis = now.second.first, f = now.first;
            vis[ver] ++;

            if (vis[T] == K)
                return dis;

            for (int i = 0; i < int(g[ver].size()); i ++) {
                int v = g[ver][i].first, w = g[ver][i].second;
                if (vis[v] < K) {
                    heap.push({dis + w + rg.d[v], {dis + w, v}});
                }
            }
        }

        return -1;
    };

    cout << a_star() << "\n";

    return 0;
}
````