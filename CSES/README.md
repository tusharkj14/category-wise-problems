## Searching and sorting
1. [Apartments](https://cses.fi/problemset/task/1084), Two pointers, Sorting, Binary search, easy.
    - Greedy.
    - Prerequisite, STL *(lower_bound, upper_bound, set, muliset)*, OR *Concept of 2 pointers*
    - Kind of classical problem.
    - 2 pointers approach
      - First Sort both arrays, keep `i` at `students choice array` and `j` at `apartments array`
      - Traverse accordingly, after making cases.
      - Relatively easier then binary search.
      - <details>
         <summary>Implementation</summary>

         ```cpp
         int n, m, k;
         cin >> n >> m >> k;
         
         std ::vector<int> arr(n), brr(m);
         
         for (auto &i : arr) cin >> i;
         for (auto &i : brr) cin >> i;
         
         int ans = 0;
         
         sort((arr).begin(), (arr).end());
         sort((brr).begin(), (brr).end());
         
         int i = 0;
         int j = 0;
         
         while (i < n and j < m) {
             int diff = abs(brr[j] - arr[i]);
             if (brr[j] < arr[i]) {
                 if (diff <= k) {
                     ans++;
                     i++, j++;
                 } else {
                     j++;
                 }
             } else {
                 if (diff <= k) {
                     ans++;
                     i++, j++;
                 } else {
                     i++;
                 }    
             }
         }
         cout << ans << '\n';

         ```
         </details>
    - Binary search approach 
      - Maintain a mulitset of apartments.
      - so that we may apply `erase`, `lower_bound`  in `logn`
      - binary search over apartments, for the user's choice. 
      - Make sure to choose smallest apartment that is fitting in user's choice.
      - Implementation (will be added soon)


2. [Subarray sums II](https://cses.fi/problemset/task/1661/), kind of map problem
    - Greedy.
    - Prerequisite, STL, or atleast how to maintain count of no.s in array.
    - Kind of classical problem.
    <details>
      <summary>Correct implementation</summary>

    ```cpp
    ll n, x; 
    cin >> n >> x;
    
    vector<ll> arr(n);
    for (auto& i: arr)
        cin >> i;
    
    map<ll, ll> mp;
    ll sum = 0;
    ll ans = 0;
    
    mp[0] = 1;
    for (int i = 0; i < n; i++) {
        sum += arr[i];
        if (mp.count(sum - x)) {
            ans += (mp[sum - x]);
        }
        mp[sum] ++;
    }
    cout << ans << '\n';
    ```
    </details>
    <details>
      <summary>wrong submission, Dont assume SUM problems to DIVISIBLITY type problems</summary>

    ```cpp
    ll n, x;
    cin >> n >> x;
    
    vll arr(n); 
    cin >> arr;
    
    map<ll, ll> mp;
    
    for (int i = 0; i < n; i++) {
        if (i > 0)
        arr[i] += arr[i - 1];
        arr[i] %= x;
        mp[arr[i]]++;
    }
    
    ll ans = 0;
    for (const auto &i : mp) {
        if (i.first == 0)
            continue;
        if (i.second >= 2) {
            ans += (i.second * (i.second - 1) / 2);
        }
    }
    ans += mp[0];
    cout << ans << '\n';
    ```
    </details>

    
## DP _(2-states, True 0-1 knapsack)_

### Visualization of iterative approach
- inside a 2D loop mainly two things going on.
     ```cpp
      1. memo[coin][s] = memo[coin - 1][s];
      2. int left = s - coins[coin - 1];
         if (left >= 0 and memo[coin - 1][left]) {
           memo[coin][s] = 1;
         }
      OR 
  
      1. if () memo[index][m] = memo[index - 1][m];
      2. else {
              memo[index][m] = max(memo[index - 1][m],
              memo[index - 1][m - prices[index - 1]] + pages[index - 1]);
          }
    ```
- what 1st statement does, if you draw `dp` table,  you will notice that it just copying previous row data in this newer row.
- 1st statement is the same as the recursive call of `not taken in account, and continuing` in terms of knapsack.
- 2nd statement on the other hand means `things picked up` in terms of 01 knapsack.
- what 2nd statement does is `from the updated data` (means after copying data in current row), it check values from previous cols, and then decided for upcoming cols of `updated rows`


1. [Removing Digits](https://cses.fi/problemset/task/1637/), easy
    - Coin change variant
    - Approach
        - Coin change problem variant.
        - In this case `fix amount` is the no. given, and `coins` are it's digits.
          <details>
          <summary> Sample Implementation </summary>

          ```cpp
           /* precomputation */
           int MAX = 1e6 + 5;
           vector<int> dp(MAX + 1, (int)1e9);
           dp[0] = 0;
           
           for (int i = 1; i < MAX; i++) {
               vector<int> tempArr;
               auto tempNum = i;
               while (tempNum) {
                   tempArr.push_back(tempNum % 10);
                   tempNum /= 10;
               }
               
               for (const auto &element : tempArr)
                 if (i - element >= 0)
                   dp[i] = min(dp[i - element] + 1, dp[i]);
           }
           /* precomputation over */
          
           int n;
           cin >> n;
           cout << dp[n] << '\n';

          ```
          </details>
    


2. [Book shop](https://cses.fi/problemset/task/1158), Classic problem, easy
    - 0-1 Knapsack without any variant
    - States are, `money and index`
      - memoisation approach
        - take a carefull look on the calling in recursive function.
          <details>
          <summary>Code sample </summary>

          ```cpp
           vector<vector<int>> memo;
           vector<int> prices, pages;
           int dp(int money, int index) {
               if (money <= 0)
               return 0;
               if (index >= pages.size())
                   return 0;
             
               int &ans = memo[index][money];
               if (ans != -1)
                   return ans;
             
               if (money < prices[index])
                   return ans = dp(money, index + 1);
        
               /* Important point */
               return ans = max(dp(money - prices[index], index + 1) + pages[index],
                               dp(money, index + 1));
           }
         
           void solve() {
               int n, money;
               cin >> n >> money;
             
               prices = pages = vector<int>(n);
               memo = vector<vector<int>>(n + 1, vector<int>(money + 1, -1));
             
               cin >> prices >> pages;
             
               int ans = dp(money, 0);
               cout << ans << '\n';
           }
          ```
          </details>
      - Iterative approach
        - do note that loop are like **First index loop then money loop**.
        - If we reverse the loop order then we will be getting **TLE**.
        - therefore **CHOOSE ORDER OF YOUR STATES CAREFULLY**.
          <details>
          <summary>Code sample </summary>

          ```cpp
           vector<vector<int>> memo;
           vector<int> prices, pages;
           
           void solve() {
               int n, money;
               cin >> n >> money;
               
               prices = pages = vector<int>(n);
               cin >> prices >> pages;
               memo = vector<vector<int>>(n + 2, vector<int>(money + 2, -1));
               
               for (int index = 0; index <= n; index++) {
                   for (int m = 0; m <= money; m++) {
                       if (m == 0 or index == 0) {
                           memo[index][m] = 0;
                           continue;
                       }
               
                      if (m < prices[index - 1]) {
                          memo[index][m] = memo[index - 1][m];
                      } else {
               
                          memo[index][m] = max(memo[index - 1][m],
                          memo[index - 1][m - prices[index - 1]] + pages[index - 1]);
                      }
                  }
               }
               cout << memo[n][money];
           }

          ```
          </details>
          <details>
          <summary>shorter Implementation </summary>

          ```cpp
           int n, target;
           cin >> n >> target;
           
           vector<int> prices(n), pages(n);
           for (auto &i : prices)
           cin >> i;
           for (auto &i : pages)
           cin >> i;
           
           vector<vector<int>> memo(n + 1, vector<int>(target + 1, 0));
           
           for (int i = 1; i <= n; i++) {
               for (int j = 0; j <= target; j++) {
                   memo[i][j] = memo[i - 1][j];
                   int rem = j - prices[i - 1];
           
                   if (rem >= 0)
                       memo[i][j] = max(memo[i][j], memo[i - 1][rem] + pages[i - 1]);
               }
           }
           cout << memo[n][target] << '\n';


          ```
          </details>

    
    
3. [Money sums](https://cses.fi/problemset/task/1745/), Base on 01 Knapsack
    - 0-1 Knapsack variant
    - coins should not be repeated. 
    - therefore coin array at top, because we don't want it to repeat.
    - States are, `money and sum`
        - iterative approach
            - take a carefull look on the data filling in 2d array.
              <details>
              <summary>Code sample </summary>

              ```cpp
              int main() {
                  int n;
                  cin >> n;
                  
                  vector<int> coins(n);
                  for (auto &i : coins)
                      cin >> i;
                  
                  int sum = accumulate(coins.begin(), coins.end(), 0);
                  vector<vector<int>> memo(n + 1, vector<int>(sum + 1, 0));
                  memo[0][0] = 1;
                  
                  for (int coin = 1; coin <= n; coin++) { // for coins array
                      for (int s = 0; s <= sum; s++) {      // for sum array
                          memo[coin][s] = memo[coin - 1][s];
                          int left = s - coins[coin - 1];
                          if (left >= 0 and memo[coin - 1][left]) {
                              memo[coin][s] = 1;
                          }
                      }
                  }
                  vector<int> ans;
                  for (int i = 1; i <= sum; i++) {
                      if (memo[n][i]) {
                          ans.push_back(i);
                      }
                  }
                  cout << ans.size() << '\n';
                  for (const auto &i : ans) {
                      cout << i << ' ';
                  }
                  cout << '\n';
              }
              ```
              </details>

4. [Two Sets II ](https://cses.fi/problemset/task/1093/), Base on 01 Knapsack
    - 0-1 Knapsack in disguise. 
    - We just have to check if we can make sum of `total_sum / 2` with given elements.
      - Now of course `total_sum / 2` won't be `integer` if `total_sum` is **odd**.
      - This brings us to `No solution` if `total_sum` is odd.
    - Observe carefully the upper limit of `outer knapsack loop`. we are only traversing upto n - 1.
      - why?
      - the moment we start taking even tha last element in our account it will bring `repititions` in our counting.
      - Every count will be `twice`.
      - By ignoring the last element we are making sure, that last one stays in `second set`.
    - States are, `Nautural No.s upto given target` and `total_sum / 2`
        - iterative approach
           <details>
           <summary>Code sample </summary>

           ```cpp
         
           long long int n;
           cin >> n;

           long long int target = n * (n + 1) / 2;
           if (target & 1) {
               cout << 0 << '\n';
               return;
           }
           target /= 2;
           vector<vector<long long int>> dp(n + 1, vector<long long int>(target + 1));
           
           dp[0][0] = 1;
           for (int i = 1; i < n; i++) { /* OUTER KNAPSACK LOOP */
               for (int j = 0; j <= target; j++) {
                   dp[i][j] += dp[i - 1][j];
                   int rem = j - i;
                   if (rem >= 0) {
                       dp[i][j] += dp[i - 1][rem];
                       dp[i][j] %= mod;
                   }
               }
           }
           cout << dp[n - 1][target] << '\n';

           ```
           </details>

5. [Grid Paths](https://cses.fi/problemset/task/1638/), classic levinshtein distance.
    - Classic problem, one of it's kind.
    - Approach
      - If `grid[i][j] == '#'` then `dp[i][j] = 0`
      - else, the same way we used to count the no. of ways in `1-D ` array.
      - This is the only recurrence relation keep filling the `dp` in the same manner.
   - <details>
     <summary>Memoization sample </summary>

     ```cpp
     std ::vector<std ::vector<char>> grid;
     std ::vector<std ::vector<long long int>> memo;
     int row;
     int dp(int i, int j) {
         if (i == 0 and j == 0)
             return (grid[i][j] == '*' ? 0 : 1);
         
         if (i < 0 or j < 0)
             return 0;
         
         auto &ans = memo[i][j];
         if (ans != -1)
             return ans;
         
         if (grid[i][j] == '*')
             ans = 0;
         else
             ans = dp(i - 1, j) % mod + dp(i, j - 1) % mod;
         return ans % mod;
     }
     
     void solve() {
         cin >> row;
         grid = std ::vector<std ::vector<char>>(row, vc(row));
         memo = std ::vector<std ::vector<long long int>>(row, vll(row, -1));
         
         for (int i = 0; i < row; i++)
             for (int j = 0; j < row; j++)
                 cin >> grid[i][j];
         
         cout << dp(row - 1, row - 1) << '\n';
     }
     ```
     </details>
   - <details>
     <summary>iterative version </summary>

      ```cpp
      int n;
      cin >> n;
      char grid[n][n];
      for (int i = 0; i < n; i++)
          for (int j = 0; j < n; j++)
              cin >> grid[i][j];
      
      vector<vector<int>> dp(n, vector<int>(n, 0));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < n; j++) {
              if (i == 0 and j == 0)
                  grid[i][j] == '.' ? dp[i][j] = 1 : dp[i][j] = 0;
              else if (i == 0)
                  grid[i][j] == '.' ? dp[i][j] = dp[i][j] % mod + dp[i][j - 1] % mod
                                    : dp[i][j] = 0;
              else if (j == 0)
                  grid[i][j] == '.' ? dp[i][j] = dp[i][j] % mod + dp[i - 1][j] % mod
                                    : dp[i][j] = 0;
              else
                  grid[i][j] == '.'
                              ? dp[i][j] = dp[i][j] % mod + dp[i - 1][j] % mod + dp[i][j - 1]
                              : dp[i][j] = 0;
              dp[i][j] %= mod;
          }
      }
      cout << dp[n - 1][n - 1] << '\n';

      ```
      </details>


6. [Edit Distance ](https://cses.fi/problemset/task/1639/), classic levinshtein distance.
    - Classic problem, one of it's kind.
    - Approach *(memo version)*
        - Base Case
          - consider we have string, `ABC` and `DC`.
          - we have to carry out our operation(insertion) on ABC
          - letsay after carrying out operation, *DC***ABC**, *DC*
          - our fist pointer `i` is at `A` of 1st string and second one `j` is at `C` of 2nd
          - since j is towards the end of the string, this brings us to the base case.
          - if (j == endOfString) OR if j < 1 : in bottom up appraoch
          - we will be returning the remaining of the `i-th string`
          - because we have yet to delete that part, hence will have to count it.
        - Now if two characters are same, then no need to `add / remove / replace` anything, just traverse `topDown / bottomUp`
        - else make your steps `inserting / removing / replacing` and take minimum.
        - [Video Resource](https://www.youtube.com/watch?v=8wv_hIRZCRg)
    - <details>
      <summary>Memoization sample </summary>

      ```cpp
      string s, t;
      std ::vector<std ::vector<int>> memo;
      int n, m;

      int editDistance(int i, int j) {
          if (i < 1)
             return j;
          if (j < 1)
             return i;
          
          int &ans = memo[i][j];
          if (ans != INF)
             return ans;
          
          if (s[i - 1] == t[j - 1])
              ans = min(ans, editDistance(i - 1, j - 1));
              else {
              int insert = 1 + editDistance(i, j - 1);
              int replace = 1 + editDistance(i - 1, j - 1);
              int del = 1 + editDistance(i - 1, j);
      
              ans = min(insert, min(replace, min(del, ans)));
          }
          
          return ans;
      }
      
      void solve() {
          cin >> s >> t;
          n = s.size();
          m = t.size();
          
          memo = std ::vector<std ::vector<int>>(n + 1, std ::vector<int>(m + 1, INF));
          cout << (editDistance(n, m) == INF ? 0 : editDistance(n, m)) << '\n';
      }

      ```
      </details>


## Graphs
1. [Course Schedule](https://cses.fi/problemset/task/1679/),
    - Intro type problem on topological sorting.
    - Approach(Kahn's algo)
        - Kahn's algo maintain an extra array to keep record of `in_degree edges`(i.e. how many receiving directed edge a node could have).
        - We then keep edges having 0 inDegree at top, in queue, and perform BFS type approach.
        - Keep subtracting the `indegree` of edge having `indegree > 0`
          -Why? *if in_degree is not 0, this indicates that there exixt some other node, which is making receiving edge to that node.*
        - 
        <details>
        <summary>Code sample (Kahn's algo)</summary>

        ```cpp
        int n, m;
        std::cin >> n >> m;
        
        std::vector<int> graph[n + 1], revGraph[n + 1], inEdge(n + 1);
        for (int i = 0; i < m; i++) {
            int a, b;
            std::cin >> a >> b;
            graph[a].push_back(b);
            revGraph[b].push_back(a);
            inEdge[b]++;
        }
        
        std::queue<int> qu;
        for (int i = 1; i <= n; i++) {
            if (inEdge[i] == 0) {
                qu.push(i);
            }
        }
        
        std::vector<int> order, dp(n + 1, 0);
        while (!qu.empty()) {
            auto u = qu.front();
            qu.pop();
            order.push_back(u);
            for (const auto &v : graph[u]) {
                    if (--inEdge[v] == 0) {
                        qu.push(v);
                    }
            }
        }
            
        dp[0] = dp[1] = 1;
        for (int i = 1; i < n; i++) {
            int u = order[i];
            for (const auto &v : revGraph[u]) {
                dp[u] += dp[v];
                dp[u] %= mod;
            }
        }    
        std::cout << dp[n] << '\n';
        int main() {
        int n, m;
        cin >> n >> m;
        
        vector<vector<int>> graph(n + 1);
        vector<int> in_degree(n + 1);
        
        for (int i = 0; i < m; i++) {
            int a, b;
            cin >> a >> b;
            graph[a].push_back(b);
            in_degree[b]++;
        }
        
        queue<int> qu;
        for (int i = 1; i <= n; i++)
            if (in_degree[i] == 0)
                qu.push(i);
        
        vector<int> order;
        while (!qu.empty()) {
            auto u = qu.front();
            qu.pop();
            
            order.push_back(u);
            for (const auto &v : graph[u]) {
                if (--in_degree[v] == 0) {
                    qu.push(v);
                }
            }
        }
        
        if (int(order.size()) != n) {
            cout << "IMPOSSIBLE\n";
        } else {
            for (const auto &i : order) {
            cout << i << ' ';
        }
        ```
        </details>
   - Approach(DFS type) *... to be updated.*



2. [Round Trip II](https://cses.fi/problemset/task/1678/),
    - Normal DFS with some coloring and keeping tracks of parents.
    - [Similar question (problem 2) and approach](https://github.com/mayankdutta/category-wise-problems/blob/main/JMI%20on%20campus/README.md)
        <details>
        <summary>Code sample </summary>

        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        
        std ::vector<std ::vector<int>> graph;
        std ::vector<int> parent;
        std ::vector<int> color;
        
        void dfs(const int &u) {
            color[u] = 1;
            for (const auto &v : graph[u]) {
                if (color[v] == 0) {
                  parent[v] = u;
                  dfs(v);
                } else if (color[v] == 1) {
                    int start = u;
                    int end = v;
        
                    std ::vector<int> ans;
                    auto temp = start;
                    while (start != end) {
                        ans.push_back(start);
                        start = parent[start];
                    }
                    ans.push_back(end);
                    ans.push_back(temp);
        
                    cout << ans.size() << '\n';
                    reverse(ans.begin(), ans.end());
                    for (const auto &i : ans) {
                        cout << i << ' ';
                    }
                    exit(0);
                }
            }
            color[u] = 2;
        }
        
        int main() {
            int n, m;
            cin >> n >> m;
            
            graph = std ::vector<std ::vector<int>>(n + 1);
            parent = std ::vector<int>(n + 1);
            color = std ::vector<int>(n + 1);
            
            for (int i = 0; i < m; i++) {
                int a, b;
                cin >> a >> b;
                graph[a].push_back(b);
            }
            
            for (int i = 1; i <= n; i++)
                if (color[i] != 2)
                    dfs(i);
            cout << "IMPOSSIBLE\n";
        }

        ```
        </details>


3. [Game Routes](https://cses.fi/problemset/task/1681), 
    - Utilizes the concept of topological sorting.
    - Innvolves basic of dynamic programming(*dont sweat, it's just fibonacci type*)
    - Approach
      - We wish to first order our edges in terms of their requirement (i.e. we are going to apply topological sort)
        - Why?, so that we can later apply dp in right order.
      - then, we will be storing `no. of ways` as DP. 
      - initializing `dp[0] = 1`, and then storing like fibonacci type. 
      - very simple dp innvolved. 
      - also we had to traverse in reverse order of directed edge, hence two graphs were taken.
        - Why?(try dry run test cases)
      - 
          <details>
          <summary>Code sample </summary>

          ```cpp
          int n, m;
          std::cin >> n >> m;
          
          std::vector<int> graph[n + 1], revGraph[n + 1], inEdge(n + 1);
          for (int i = 0; i < m; i++) {
              int a, b;
              std::cin >> a >> b;
              graph[a].push_back(b);
              revGraph[b].push_back(a);
              inEdge[b]++;
          }
          
          std::queue<int> qu;
          for (int i = 1; i <= n; i++) {
              if (inEdge[i] == 0) {
                  qu.push(i);
              }
          }
          
          std::vector<int> order, dp(n + 1, 0);
          while (!qu.empty()) {
              auto u = qu.front();
              qu.pop();
              order.push_back(u);
              for (const auto &v : graph[u]) {
                      if (--inEdge[v] == 0) {
                          qu.push(v);
                      }
              }
          }
              
          dp[0] = dp[1] = 1;
          for (int i = 1; i < n; i++) {
              int u = order[i];
              for (const auto &v : revGraph[u]) {
                  dp[u] += dp[v];
                  dp[u] %= mod;
              }
          }    
          std::cout << dp[n] << '\n';
          ```
          </details>
      
    
      
4. [Investigation](https://cses.fi/problemset/task/1202/), Medium
    - modified Djikstra, slight dp.
    - Approach
        - Idea is to save everything as we proceed.
        - Simple dp innvolved.
        <details>
        <summary>Code sample </summary>

        ```cpp
        #define Graph vector<vector<pair<long long int, long long int>>>
        
        struct info {
            int from, to, w;
        };
        void solve() {
            ll n, m;
            cin >> n >> m;
            
            Graph graph(n + 2);
            for (int i = 0; i < m; i++) {
                ll a, b, w;
                cin >> a >> b >> w;
                graph[a].push_back({b, w});
            }
            
            priority_queue<pll, vector<pair<ll, ll>>, std::greater<pair<ll, ll>>> pq;
            vll dist(n + 1, INF);
            vll ways(n + 1, 0);
            vll mn(n + 1, INF);
            vll mx(n + 1, 0);
            
            pq.push({0, 1});
            dist[1] = 0;
            ways[1] = 1;
            mn[1] = 0;
            mx[1] = 0;
            
            while (!pq.empty()) {
                auto [d, father] = pq.top();
                pq.pop();
            
                if (dist[father] < d)
                  continue;
            
                for (const auto &[son, dd] : graph[father]) {
                  if (dist[father] + dd < dist[son]) {
                    dist[son] = dist[father] + dd;
                    ways[son] = ways[father] % mod;
                    mn[son] = mn[father] + 1;
                    mx[son] = mx[father] + 1;
            
                    pq.push({dist[son], son});
                  } else if (dist[father] + dd == dist[son]) {
                    ways[son] = (ways[son] % mod + ways[father] % mod) % mod;
                    mn[son] = min(mn[father] + 1, mn[son]);
                    mx[son] = max(mx[father] + 1, mx[son]);
                  }
                }
            }
            
            cout << dist[n] << ' ';
            cout << ways[n] << ' ';
            cout << mn[n] << ' ';
            cout << mx[n] << ' ';
        }
        ```
        </details>
    
    
5. [Fligh discount](https://cses.fi/problemset/task/1195/), Medium
    - modified Djikstra
    - Approach
        - Idea is to find the distance of all the nodes from `source` and from `destination`
            - We then can do something like,
            - `ans = min(ans, dist[source -> curr_node] + dist [curr_node -> destination] + weight_of_current_node / 2`
            - meaning, we are providing chance to every node, to add **HALF** of it's weight
        - We will be forming `Two graphs`, since given graph is `directed`.
        - And we will be preparing two diff. `dist` arrays, to store distance, one from `source side` and another from `destination side`.
        <details>
        <summary>Code sample </summary>

        ```cpp
        #define Graph vector<vector<pair<long long int, long long int>>>
        Graph graph, graphRev;
        
        vector<ll> dji(const ll &source, const Graph &graph) {
            ll n = graph.size();
            vector<ll> dist(n + 1, 2e18);
            
            priority_queue<pll, vector<pll>, greater<pll>> pq;
            pq.push({0, source});
            dist[source] = 0;
            
            while (!pq.empty()) {
                auto [d, u] = pq.top();
                pq.pop();
            
                if (dist[u] < d)
                  continue;
            
                for (const auto &[v, dd] : graph[u]) {
                  if (dd + dist[u] < dist[v]) {
                    dist[v] = dd + dist[u];
                    pq.push({dist[v], v});
                  }
                }
            }
            return dist;
        }
        
        void solve() {
            ll n, m;
            cin >> n >> m;
            
            graph = graphRev = Graph(n + 1);
            vector<tuple<ll, ll, ll>> arr;
                for (int i = 0; i < m; i++) {
                ll a, b, w;
                cin >> a >> b >> w;
                graph[a].push_back({b, w});
                graphRev[b].push_back({a, w});
            
                arr.push_back({a, b, w});
            }
            
            auto one = dji(1, graph);
            auto two = dji(n, graphRev);
            
            ll ans = ll(2e18);
            for (const auto &[from, to, w] : arr) {
                ans = min(ans, one[from] + two[to] + (w) / 2);
            }
            cout << ans << '\n';
        }
        ```
        </details>
    
    
6. [Graph girth](https://cses.fi/problemset/task/1707/), Medium
    - modified BFS
    - Approach
        - Idea is to find the distance of all the nodes from every node, one by one. 
        - Yes it is `O(n * (n + m))`
        - If you are thinking something like [this](https://github.com/mayankdutta/Examples/blob/main/graphs/adj/cycle_in_graph.cpp), this will give error.
        - In this graph there are multiple edges along with many cycle, if their were only one cycle then above one will do fine.
        - Here Every node has MULTIPLE PARENTS.
        <details>
        <summary>Code sample </summary>

        ```cpp
      
       int n, m;
       cin >> n >> m;
       vector<vector<int>> graph(n);
       for (int i = 0; i < m; i++) {
           int a, b;
           cin >> a >> b;
           a--, b--;
           graph[a].push_back(b);
           graph[b].push_back(a);
       }
       
       int ans = INT_MAX;
       
       for (int i = 0; i < n; i++) {
           vector<int> dist(n, -1);
           queue<int> qu;
       
           qu.push(i);
           dist[i] = 0;
       
           while (!qu.empty()) {
             auto u = qu.front();
             qu.pop();
       
             for (const auto &v : graph[u]) {
               if (dist[v] == -1) {
                 dist[v] = dist[u] + 1;
                 qu.push(v);
               } else if (dist[v] >= dist[u]) {
                 ans = min(ans, dist[u] + dist[v] + 1);
               }
             }
           }
       }
       cout << (ans == INT_MAX ? -1 : ans) << '\n';
       

        ```
        </details>

7. [Longest Flight Route](https://cses.fi/problemset/task/1680), Medium
    - Pre Requisites - Topological Sorting (Kahn's Algorithm)
    - Approach
        - Idea is to do topological sorting twice to get the longest route from 1 to n, once without taking node "1" in consideration and then a topo sort from node "1" itself.
        - Dijkstra or modified BFS shortest path might not work.
        - In this graph there are only directed edges with no cycle.
        - Using Kahn's Algortihm for topo sort, we can easily find the longest route in a DAG.
        - the main edge case is finding whether we can visit the node 'n' without using node '1', in which case the answer would be "IMPOSSIBLE".
        - else follow the simple bfs path traversed using the topo sort.
        <details>
        <summary>Code sample </summary>

        ```cpp
      
       void solve(){
          ll n, m;    cin >> n >> m;
          vector<ll> adj[n+1];
          vector<ll> id(n+1);

          while(m--){
              ll u, v;    cin >> u >> v;
              adj[u].push_back(v);
              id[v]++;
          }
          unordered_set<ll> topo;

          vector<ll> par(n+1, -1);
          queue<ll> bfs;

          for (ll i = 2; i<=n; i++){
              if (id[i] == 0) bfs.push(i);
          }


          while(bfs.size()){
              ll u = bfs.front();     bfs.pop();
              topo.insert(u);
              for (auto v: adj[u]){
                  id[v]--;
                  if (id[v] == 0) {
                      bfs.push(v);
                      par[v] = u;
                  }
              }
          }

          if (id[n] == 0 and topo.find(1) == topo.end())  {
              cout << "IMPOSSIBLE\n";
              return;
          }

          bfs.push(1);

          while(bfs.size()){
              ll u = bfs.front();     bfs.pop();
              topo.insert(u);
              for (auto v: adj[u]){
                  id[v]--;
                  if (id[v] == 0) {
                      bfs.push(v);
                      par[v] = u;
                  }
              }
          }


          if (par[n] == -1)     cout << "IMPOSSIBLE\n"; 
          else{
              vector<ll> temp;
              temp.push_back(n);
              while(temp.back() != 1){
                  temp.push_back(par[temp.back()]);
              }
              reverse(temp.begin(), temp.end());

              cout << temp.size() << endl;
              for (auto &i: temp){
                  cout << i << " ";
              }
          }
      }
       

        ```
        </details>

