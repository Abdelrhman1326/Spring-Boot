
## Cycle detection using DFS in graph

```  c++
vector<vector<int>> gr;  
vector<int> vis;  
int n, m;  
  
void dfs(int node) {  
    // mark node as opened:  
    vis[node] = 1;  
  
    for (auto child : gr[node]) {  
        if (vis[child] == 1) {  
            cout << "1\n";  // cycle detected, "opened node is visited again"
            exit(0);  
        }  else if (vis[child] == 0) {  // Unvisited node  
            dfs(child);
        }
    }  
    vis[node] = 2;
}  
  
int main() {  
    fio();  
    cin >> n >> m;  
    gr.resize(n, vector<int> ());  
    vis.assign(n, 0);  
    for (int i = 0; i < m; i++) {  
        int u, v;  
        cin >> u >> v;  
        gr[u].push_back(v);  
    }  
    for (int i = 0; i < n; i++) {  
        if (!vis[i]) {  
            dfs(i);  
        }  
    }  
    cout << 0 << endl;  
    return 0;  
}
```
