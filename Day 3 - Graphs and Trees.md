## Topics :

### **Introduction to Graphs and Trees**

Session Notes : 

- Intro Notes : https://drive.google.com/file/d/15P7H9XcegXFKMW9_yUlOKB0MiDSpaGSg/view?usp=sharing
- Traversal / DSU Notes :  https://drive.google.com/file/d/13iVboTd0uUv7fCEYeJt9UD4PXfT21Tab/view?usp=sharing


**Notes And Resources :**

- https://csacademy.com/lesson/introduction_to_graphs
- **USACO** Book : https://darrenyao.com/usacobook/cpp.pdf#page=41
- Graph Visualiser : https://csacademy.com/app/graph_editor/
- DSU Visualiser : https://www.cs.usfca.edu/~galles/JavascriptVisual/DisjointSets.html

##### Representing a Graph as a Struct Node 
```cpp

struct node{

    int next , weight;
    node(){
        next = -1;
        weight = 0;
    }

    node(int x){
        next = x ;
        weight = 0;

    }
    node(int x, int y){
        next = x ;
        weight = y;
    }
};

	int n, m; // n -> nodes, m -> edges

    cin >> n >> m ;

    vector<node> adj[n+1];

    for(int i = 0 ; i < m ; i ++ ){

        int x, y , w;

        cin >> x >> y >> w;

        adj[x].push_back(node(y, w));

        adj[y].push_back(node(x, w));

    }

```

**Terminalogies Pertaining to Graph**




**Traversals** : DFS / BFS 

**DFS:**

```cpp
	function<void(int)> dfs = [&]( int node ){
		
		vis[node] = true;
		
		cout << node << " ";
		
		for( auto next : graph[node] ){
			if( !vis[next] ){
				vis[next] = true;
				dfs(next);
			}
		}
		
	};
```

**BFS**

```cpp
	function<void(int)> bfs = [&]( int node ){
		
		queue<int> q;
		q.push(node);
		
		while( q.size() ){
			
			
			int size = q.size();
			
			for( int i = 0; i < size ; i++ ){
				
				int now = q.front(); q.pop();
				
				cout << now << " ";
				
				vis[now] = 1;
				
				for( auto next : graph[now] ){
					if( !vis[next] ){
						vis[next] = 1;
						q.push(next);
					}
				}
			}
			
			cout << " level ended " ;
		}
	};
```

**Diameter of a Tree using DFS** 

```cpp
class Tree {
private:
    vector<vector<int>> adj;
    int diameter;

public:
    Tree(int n) {
        adj.resize(n);
        diameter = 0;
    }

    void addEdge(int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    int dfs(int u, int parent, function<void(int, int)> visit) {
        int maxDepth = 0;
        for (int v : adj[u]) {
            if (v != parent) {
                int depth = dfs(v, u, visit);
                maxDepth = max(maxDepth, depth);
            }
        }
        visit(u, maxDepth);
        return maxDepth + 1;
    }

    int getDiameter() {
        dfs(0, -1, [this](int u, int maxDepth) {
            diameter = max(diameter, maxDepth);
        });
        return diameter;
    }
};
```

**Using TIN and TOUT for BFS Traversals** 

```cpp

	int timer = 0;

	function<void(int)> dfs_with_timer = [&]( int node ){
		
		vis[node] = true;
		
		tin[node] = timer;
		timer += 1;
		
		cout << node << " ";
		
		for( auto next : graph[node] ){
			if( !vis[next] ){
				vis[next] = true;
				dfs(next);
			}
		}
		
		tout[node] = timer++;
		
	};
	
```

**Disjoint Set Union** : C++ Code

```cpp
struct DSU {
    int n;
    vector<int> par, sz;
    
    DSU(int t_n) : n(t_n), par(t_n), sz(t_n) {
        iota(par.begin(), par.end(), 0);
        fill(sz.begin(), sz.end(), 1);
    }
 
    int find_parent(int x) {
        if (par[x] == x) {
            return x;
        }
        return par[x] = find_parent(par[x]);
    }
 
    bool merge(int u, int v) {
        int pu = find_parent(u), pv = find_parent(v);
        if (pu == pv) return false;
        if (sz[pu] < sz[pv]) swap(pu, pv);
        par[pv] = pu;
        sz[pu] += sz[pv];
        return true;
    }
 
    bool isSameParent(int u, int v) {
        return find_parent(u) == find_parent(v);
    }
 
    int getSize(int x) {
        int px = find_parent(x);
        return sz[px];
    }
};
```















