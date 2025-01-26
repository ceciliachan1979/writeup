## Linear programming formulation

Max-flow problem 可以看成是一個 linear programming problem。

```
max sum of flow from source
subject to flow conservation and capacity constraints
```

明顯 objective 和 constraint 都是 linear 的。

我們之前抇提過 max-flow min-cut 是 dual type problem，那 min-cut 會不會是 max-flow 的 dual formulation 呢？

### Dual formulation

在 form dual 之前，我們複習一下 linear programming 的 dual formulation method。

- primal 的 constraint 變成 dual 的 variable﹐dual variable 的方向和 primal constraint 的方向是相反的。一個 equality constraint 變成一個 unbounded variable，一個 $\ge 0$ 的 constraint 變成一個 $\le 0$ 的 variable，一個 $\le 0$ 的 constraint 變成一個 $\ge 0$ 的 variable。

- 有了 dual variable，就可以講 dual objective，dual 的 objective 的 coefficient 就是 primal 的 constraint vector。

- 接下來，primal 的 variable 變成 dual 的 constraint，dual constraint 的方向和 primal variable 的方向是相同的。一個 lower bounded by 0 的 primal variable 會變成一個 $\ge 0$ 的 constraint，一個 unbounded primal variable 會變成一個 equality constraint，一個 upper bounded by 0 的 primal variable 會變成一個 $\le 0$ 的 constraint。

- 最後是 dual constraint 的 coefficient，它的左手邊就是原來 constraint matrix 的 values，它的右手邊是 primal 的 objective vector。

### Dual of max-flow

首先，我們把 $V - \{s,t\}$ 叫做 $N$ ，這樣可以簡化一下 notation。

對於每一個在 $N$ 中的 node $v$ ，我們都有一條 flow conservation 的 constraint，所以它們的 dual variable 叫做 $y_v$ 。flow conservation 是 equality constraint，所以 dual variable 是 unbounded 的。

對於每一條 edge $(u, v)$ ，我們都有一條 capacity constraint，所以它們的 dual variable 叫做 $x_{uv}$ 。capacity constraint 是 upper bound 的，所以 dual variable 是 $\ge 0$ 的。

Dual 的 objective function 是 $\sum_{((u, v) \in E)} x_{uv} c_{uv}$ ，其中 $c_{uv}$ 是 edge $(u, v)$ 的 capacity，而 $y_v$ 的 coefficient 是 $0$ 所以不用寫出來。

現在我們開始看到 min-cut 的影子了，我們想要 minimize sum of capacities。

接下來是 dual 的 constraint，對於每一個 edge $(u, v)$ ，我們有一個 primal variable $f_{uv}$ ，所以我們有一個 dual constraint。這些 primal variable 都是 $\ge 0$ ，所以 dual constraint 都是 $\ge x$ ， $x$ 是 primal objective 中 $f_{uv}$ 的 coefficient。如果 $u$ 是 $s$ ， $x$ 就是 1，否則 $x$ 就是 0。

最後是 dual constraint 的左手邊。每一個非 source 非 sink 的 flow variable $f_{uv}$ 在 primal 中出現 exactly 三次

- 第一次是 flow conservation， $\sum\limits_{w}f_{wu} - \sum\limits_{v}f_{uv} = 0$ ，所以 dual constraint 中 $y_u$ 的 coefficient 是 $-1$ 。
- 第二次還是 flow conservation， $\sum\limits_{v}f_{uv} - \sum\limits_{w}f_{vw} = 0$ ，所以 dual constraint 中 $y_v$ 的 coefficient 是 $1$ 。
- 第三次是 capacity， $f_{uv} \le c_{uv}$ ，所以 dual constraint 中 $x_{uv}$ 的 coefficient 是 $1$ 。

所以 dual constraint 就是 $-y_u + y_v + x_{uv} \ge 0$ 。

對於 source flow，它只有第二次的 flow conservation，所以 dual constraint 是 $x_{sv} + y_v \ge 1$ 。

對於 sink flow，它只有第一次的 flow conservation，所以 dual constraint 是 $x_{vt} - y_v \ge 0$ 。

如果存在 source 到 sink 的直接 link，那 dual constraint 就是 $x_{st} \ge 1$ 。

總結一下，整個 dual formulation 是

minimize $\sum_{((u, v) \in E)} x_{uv} c_{uv}$ 

subject to

 $-y_u + y_v + x_{uv} \ge 0$ for each not source/sink edge $(u, v)$ 

 $x_{sv} + y_v \ge 1$ for each edge $(s, v)$ 

 $x_{vt} - y_v \ge 0$ for each edge $(v, t)$ 

 $x_{st} \ge 1$ for edge $(s, t)$ if exists

 $x_{uv} \ge 1$ for each edge $(u, v)$ 

 $y_v \in \mathbb{R}$ for each node $v \in N$ 

## dual 為甚麼是 min-cut？

我們先假設 $y$ 的值不是 0 就是 1。

- 如果 $u, v \in N$ ，那麼如果 $y_u = y_v$ ，則 $x_{uv} = 0$ ，否則 $x_{uv} \ge y_v - y_u$ = 1，最小化會讓 $x_{uv} = 1$ 
- 如果 $(s, v) \in E$ ，那麼如果 $y_v = 1$ ，則 $x_{s,v} = 0$ ，否則 $x_{sv} \ge 1 - y_v$ = 1，最小化會讓 $x_{sv} = 1$ 
- 如果 $(v, t) \in E$ ，那麼如果 $y_v = 0$ ，則 $x_{v,sink} = 0$ ，否則 $x_{vt} \ge y_v$ = 1，最小化會讓 $x_{vt} = 1$ 

所以 $y_u$ 可以理解成一個 node $u$ 是不是在 $S$ 的一個 indicator，只要 $y_u = 1$ ，那麼 $u$ 就在 $S$ 裡面， $x_{uv}$ 可以理解成一個 edge $(u, v)$ 是不是在 cut 裡面，只要 $x_{uv} = 1$ ，那麼 $(u, v)$ 就在 cut 裡面。

> 唯一的問題是 $y$ 為甚麼是 0/1？

這個 [博客](https://www.brentaustgen.com/blogs/maxflow-mincut-duality-1/) 裡有答案，主要的 idea 有兩個
- 加一條由 sink 去 source 的 edge，就可以令到 constraint 簡化很多，沒有 source/sink 的 special cases。
- 用 complementary slackness 可以 argue $y$ 是 0/1。

Stanford 的 [solution](https://theory.stanford.edu/~trevisan/cs261/lecture15.pdf) 比這個更好，即使有 exponential 個 primal variables，dual 也可以寫的。

