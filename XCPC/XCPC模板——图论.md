
### 图论

#### 基本概念

+ 树的直径：最远的两个点之间的距离称为树的直径，连接两个点的路径被称为树的最长链。
+ 图的连通性：对于有向图而言，存在强连通和非强连通的区别，对于无向图而言，分为连通和非连通的区别。强连通是指任意两个点之间都可以互相达到。
+ 无向图的割边和割点：割边指的是如果把这条边删除，那么这个图的联通分量的个数会增加，割点指的是如果把这个点删除，这个图的连通分量的个数也会增加。
+ 树的dfn(时间戳)序列：指的是对树进行一次dfs得到的每个点第一次被访问到的时间戳。
+ 树的low(追溯值)序列，指的是以x为根的子树里面，这些子节点（包括自身的时间戳的最小值）

#### 常用模板

+ Tarjan算法求lca

  ```C++
  void tarjan(int x){
      v[x]=1;
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(v[y]) continue;
          tarjan(y);
          fa[y]=x;
      }
      // 进行离线操作
      for(int i=0;i<query[x].size();i++){
          int y=query[x][i],id=query_id[x][i];
          if(v[y]==2){
              int lca=get(y); // lca就是x和y的最近公共祖先
          }
      }
      v[x]=2;
  }
  ```
+ Tarjan算法求割边

  ```C++
  void tarjan(int x,int in_edge){
      dfn[x]=low[x]=++num;
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(!dfn[y]){
              tarjan(y,i);
              low[x]=min(low[x],low[y]);
              if(low[x]>dfn[y]){
                  bridge[i]=bridge[i^1]=true;
              }
          }else if(i!=(in_edge^1)){
              low[x]=min(low[x],dfn[y]);
          }
      }
  }
  
  
  // 调用
  for(int i=1;i<=n;i++){
      if(!dfn[i]){
          tarjan(i,0);
      }
  }
  ```

+ Tarjan算法求割点

  ```C++
  void tarjan(int x){
      dfn[x]=low[x]=++num;
      int flag=0; // 记录子节点的个数
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(!dfn[y]){
              low[x]=min(low[x],low[y]);
      	    if(low[x]>=dfn[y]){
  	            flag++;
              	if(x!=root||flag>1) cut[x]=true;
          	}
          }else{
              low[x]=min(low[x],dfn[y]);
          }
      }
  }
  
  
  // 调用
  for(int i=1;i<=n;i++){
      if(!dfn[i]){
          root=i;
          tarjan(i);
      }
  }
  ```

  

#### 欧拉回路问题

+ 欧拉路：如果存在一条路径从s到t，恰好不重不漏地经过每一条边一次，那么这个从s到t的路径就被称作欧拉路。
+ 欧拉回路：欧拉回路就是在欧拉路的基础上，从起点出发可以回到终点。

+ 欧拉图的判定
	+ 一张无向图为欧拉图，当且仅当无向图连通，并且每个点的度数都是偶数。
+ 欧拉路的存在性判定
	+ 在一张无向图中存在欧拉路，当且仅当无向图连通，并且图中存在恰好两个结点的度数为奇数，其他结点的度数都是偶数，这两个度数为奇数的结点就是欧拉回路的起点和终点。
	
	+ 找出欧拉回路
	
	  ```C++
	  void dfs(int x){
	      对于从x出发的每条边(x,y)
	          如果这条边没有被访问过
	          	把这条边标记为已经访问
	          	dfs(y)
	          	把y入栈
	  }
	  
	  在主函数中
	      dfs(1)
	      倒序输出栈里面的所有的结点
	  ```
	
	+ 欧拉回路的代码
	
	  ```C++
	  void euler(){
	      stack[++top]=1;
	      while(top>0){
	          int x=stack[top],i=head[i];
	          while(i&&vis[i]) i=nxt[i];  // 找到第一条没有被访问的边
	          if(i){  // 沿着这条边模拟递归的过程，标记改边，b
	              stack[++top]=ver[i];
	              vis[i]=vis[i^1]=true;
	              head[x]=nxt[i];
	          }else{
	              top--;
	              ans[++t]=x;
	          }
	      }
	  }
	  
	  int main(){
	      cin>>n>>m;
	      tot=1;
	      for(int i=1;i<=m;i++){
	          int x,y;
	          cin>>x>>y;
	          add(x,y);
	          add(y,x);
	      }
	      euler();
	      for(int i=t;i;i--){
	          cout<<ans[i]<<endl;
	      }
	  }
	  ```
	
	  
