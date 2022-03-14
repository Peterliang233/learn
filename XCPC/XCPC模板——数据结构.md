

### 数据结构

#### 单调栈

+ 单调栈的使用是插入一个数的时候，保持栈的单调性的情况下弹出栈里面最少的元素。
+ 当我们往单调栈插入数据的时候，我们可以保持这个栈按照某一单调性，方法就是将这个数和栈顶的元素之间进行比较。

```C++
typedef long long ll;
struct node{
    int val;
    int index;
}Node[3000010];
stack<node> st;
ll id[3000010];
int main() {
    //freopen("test.in","r",stdin);
    int n;
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d",&Node[i].val);
        Node[i].index=i;
        id[i]=0;
    }
    for(int i=1;i<=n;i++){
        if(st.empty()){  //判断栈是否为空
            st.push(Node[i]);
        }else{
            if(st.top().val>Node[i].val){
                st.push(Node[i]);
            }else{
                while(st.size()&&st.top().val<Node[i].val){
                    id[st.top().index]=i;
                    st.pop();
                }
                st.push(Node[i]);
            }
        }
    }
    for(int i=1;i<=n;i++){
        printf("%d ",id[i]);
    }
    puts("");
    fclose(stdout);
    return 0;
}
```

#### 单调队列

+ 单调队列是维护区间内连续的k个值的最值的问题。

+ 比如，我们要维护一个区间内的最大值的时候，我们维护的其实就是一个单调递减的队列，所以，每次我们只需要从这个序列里面拿出队首的那个元素就是当前的最值了。而且，对于每次符合队列的元素，我们将这些元素都放到队尾进行一个维护，因为这些元素后面也可能成为对手元素。

+ 维护最小值的也是同样的想法，我们维护一个单调递增的队列，每次我们只需要找出队首的那个元素就行了。

  ```C++
  const int maxn=1000010;
  int a[maxn],q[maxn];
  int n,k;
  void getmx(){
      int head=0,tail=0;
      for(int i=1;i<=k;i++){
          while(head<=tail&&a[q[tail]]<=a[i]) tail--;
          q[++tail]=i;
      }
      for(int i=k;i<=n;i++){
          while(head<=tail&&a[q[tail]]<=a[i]) tail--;
          q[++tail]=i;
          while(q[head]+k<=i) head++;
          printf("%d ",a[q[head]]);
      }
      printf("\n");
  }
  
  void getmi(){
      int head=0,tail=0;
      for(int i=1;i<k;i++){
          while(head<=tail&&a[q[tail]]>=a[i]) tail--;
          q[++tail]=i;
      }
      for(int i=k;i<=n;i++){
          while(head<=tail&&a[q[tail]]>=a[i]) tail--;
          q[++tail]=i;
          while(q[head]+k<=i) head++;
          printf("%d ",a[q[head]]);
      }
      printf("\n");
  }
  int main() {
      //freopen("test.in","r",stdin);
      scanf("%d%d",&n,&k);
      for(int i=1;i<=n;i++){
          scanf("%d",&a[i]);
      }
      getmi();
      getmx();
      fclose(stdout);
      return 0;
  }
  
  ```

#### ST表(倍增表)

+ ST表可以解决RMD算法。也就是维护区间内某一个属性的值，如gcd，最值等，缺点是不能进行区间的修改。

+ ST表是将一个int范围内的数进行二进制划分，我们可以发现，最多也就20或者30个区间,f[i] [j]表示的是区间[i,i+2^j-1]内维护的某一个值。那么，我们发现，如果要求查询某一个区间的时候，我们可以将区间进行划分，我们利用两个区间重叠之后覆盖整个区间的特点尽可能的去覆盖两个区间。
+ 首先，我们要进行的是预处理出每个点倍增一定的长度的值，然后就可以很容易地进行状态方程的转移了。

```C++
inline int read() {
  char c = getchar();
  int x = 0, f = 1;
  while (c < '0' || c > '9') {
    if (c == '-') f = -1;
    c = getchar();
  }
  while (c >= '0' && c <= '9') {
    x = x * 10 + c - '0';
    c = getchar();
  }
  return x * f;
}

const int logn=21;
const int maxn=2000010;
int f[maxn][30],lg[maxn+1];
void pre(){
    lg[1]=0;
    lg[2]=1;
    for(int i=3;i<=maxn;i++){
        lg[i]=lg[i/2]+1;
    }
}

int main() {
    //freopen("test.in","r",stdin);
    int n=read(),m=read();
    for(int i=1;i<=n;i++){
        f[i][0]=read();
    }
    pre();
    for(int j=1;j<=logn;j++){
        for(int i=1;i+(1<<j)-1<=n;i++){
            f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
        }
    }
    while(m--){
        int x=read(),y=read();
        int s=lg[y-x+1];
        printf("%d\n", max(f[x][s],f[y-(1<<s)+1][s]));
    }
```

##### 倍增+二分模板

+ 倍增一般是用于预处理的时候，但是我们发现这个一般只用来进行预处理操作，对于快读查询一个大的区间，我们可以使用枚举左端点，更新右端点的方法进行处理，下面给出一个倍增+二分的方法查询区间的GCD。

  ```C++
  #include<bits/stdc++.h>
  
  using namespace std;
  typedef long long ll;
  
  ll a[200010],d[200010],f[200010][30];
  int n;
  
  ll gcd(ll x,ll y){
  	if(y==0) return x;
  	return gcd(y,x%y);
  }
  
  void ST(){
  	for(int i=1;i<=n;i++){
  		f[i][0]=d[i];
  	}
  	
  	int t=log(n)/log(2)+1;
  	
  	for(int j=1;j<t;j++){
  		for(int i=1;i<=n-(1<<j)+1;i++){
  			f[i][j]=gcd(f[i][j-1],f[i+(1<<(j-1))][j-1]);
  		}
  	}
  }
  
  ll query(int l,int r){
  	int k=log(r-l+1)/log(2);
  	
  	return gcd(f[l][k],f[r-(1<<k)+1][k]);
  }
  
  bool check(int l,int mid){
  	ll g=query(l,mid);
  	if(g>1) return true;
  	return false;
  }
  
  void solve(){
  	scanf("%d",&n);
  	for(int i=1;i<=n;i++){
  		scanf("%lld",&a[i]);
  	}
  		
  	for(int i=2;i<=n;i++){
  		d[i-1]=abs(a[i]-a[i-1]);
  	}
  	
  	n--;
  	
  	ST();
  
  	int ans=0;
      // 二分模板，枚举左端点，e
  	for(int i=1;i<=n;i++){
  		if(d[i]==1) continue;
  		int l=i,r=n;
  		while(l<r){
  			int mid=(l+r+1)>>1;
  			if(check(i,mid)==false)  r=mid-1;
  			else l=mid;
  		}
  		
  		ans=max(ans,l-i+1);
  	}
  	
  	printf("%d\n",ans+1);
  }
  
  int main(){
  	int t;
  	scanf("%d",&t);
  	while(t--){
  		solve();
  	}
  	return 0;
  }
  ```

  



#### 树状数组

+ 树状数组的代码想对于线段树来说更加精简，但是树状数组一般支持的是单点修改，在我看来，树状数组其实和倍增的思想很一致，就是将在一个范围内的区间进行二进制划分，然后我们每次对某一个点的修改，我们只需要更新涉及这个位置的相关的区间就行了，这样我们就可以将时间复杂度降低为log级别的了。

+ 首先，我们介绍一个lowbit函数，这个函数是找出一个数的二进制表示下的最低位的1。

  ```C++
  int lowbit(int x){
  	return x*(-x);
  }
  ```

+ 然后，如果想要对某一个位置的数进行修改的话，那么我们就只需要后进行二进制区间的更新;如果想要对某一个区间范围进行查询的话，我们只需要利用前缀和的思想进行一个处理。往前面进行查找。

##### 常用模板

+ 存一个板子，获取存在**绝对众数**的区间的个数模板

  ```C++
  #include <bits/stdc++.h>
  using namespace std;
  typedef long long LL;
  const int INF = 0x3f3f3f3f;
  const LL mod = 1e9 + 7;
  const int N = 500005;
  // 修改差分 来维护前缀和的前缀和
  // c1 为差分d c2为d*i  c3 为d*i*i
  LL c1[N * 2], c2[N * 2], c3[N * 2];
  LL sum(int x) {
      LL res = 0;
      for (int i = x; i > 0; i -= i & -i) {
          res += c1[i] * (x + 2) * (x + 1) - c2[i] * (2 * x + 3) + c3[i];
      }
      return res / 2;
  }
  void add(int x, LL d, int n) {
      for (int i = x; i <= n; i += i & -i) {
          c1[i] += d;
          c2[i] += d * x;
          c3[i] += d * x * x;
      }
  }
  int a[N];
  vector<int> b[N];
  int main() {
      int n;
      scanf("%d%*d", &n);
      for (int i = 1; i <= n; i++) {
          scanf("%d", &a[i]);
          b[a[i]].push_back(i);
      }
      const int wc = n + 1;  // 偏移量，把[-n,n] 平移到 [1,2n+1]
      LL ans = 0;
      for (int i = 0; i < n; i++) {
          b[i].push_back(n + 1);
          int last = 0;
          for (int j = 0; j < b[i].size(); j++) {
              int y = 2 * j - last + wc, x = 2 * j - (b[i][j] - 1) + wc;
              // 查询 sum([1,t-1] 的权值和), 其中t在[x,y]范围内，
              ans += sum(y - 1) - (x >= 3 ? sum(x - 2) : 0);
              // [x,y] 这些数的权值+1
              add(x, 1, 2 * n + 1);
              add(y + 1, -1, 2 * n + 1);
              last = b[i][j];
          }
          last = 0;
          for (int j = 0; j < b[i].size(); j++) {
              int y = 2 * j - last + wc, x = 2 * j - (b[i][j] - 1) + wc;
              add(x, -1, 2 * n + 1);
              add(y + 1, 1, 2 * n + 1);
              last = b[i][j];
          }
      }
      printf("%lld\n", ans);
      return 0;
  }
  ```

  

#### 线段树

+ 线段树的操作大体分为建树，更新，查询，懒标记等等。建树和更新是比较常见的，对于懒标记，根据具体的题目进行处理，比如进行区间反转等等，可以使用懒惰标记进行标记，根据懒惰进行区间的更新。
+ 如果出现一个变量比较难维护的情况，可以使用多个变量进行维护，但是要确保这些变量都是可以进行区间的合并的，即有区间的合并性。
+ 懒标记是我们经常需要使用的一个应用，为了防止没必要的更新子节点的信息，我们先使用懒标记进行处理，如果真的需要用到这个结点再进行使用。

##### 扫描线

+ 线段树有一个比较常见的问题就是扫描线问题，求矩形的并等

+ ```C++
  #include<bits/stdc++.h> 
  
  
  using namespace std;
  typedef long long ll;
  
  const int N=10010;
  
  struct node{
  	int l,r;
  	int cnt;
  	double len;
  }Node[N<<3];
  
  struct point{
  	double x,y1,y2;
  	int flag;
  }p[N<<2];
  
  double raw[N<<1];
  map<double,int> val;
  
  bool cmp(point a,point b){
  	return a.x<b.x;
  }
  
  void build(int u,int l,int r){
  	Node[u].l=l,Node[u].r=r,Node[u].cnt=0,Node[u].len=0;
  	if(l==r) return;
  	int mid=(l+r)>>1;
  	build(u<<1,l,mid);
  	build(u<<1|1,mid+1,r);
  }
  
  
  void change(int u,int l,int r,int k){
  	if(l<=Node[u].l&&r>=Node[u].r){
  		Node[u].len=((Node[u].cnt+=k)?raw[Node[u].r+1]-raw[Node[u].l]:(Node[u].l==Node[u].r?0:Node[u<<1].len+Node[u<<1|1].len));
  		return;
  	}
  	
  	int mid=(Node[u].l+Node[u].r)>>1;
  	if(l<=mid) change(u<<1,l,r,k);
  	if(r>mid)  change(u<<1|1,l,r,k);
  	Node[u].len=(Node[u].cnt?raw[Node[u].r+1]-raw[Node[u].l]:Node[u<<1].len+Node[u<<1|1].len);
  }
  
  int main(){
  	int n;
  	int k=0;
  	while(cin>>n&&n){
  		val.clear();
  		k++;
  		printf("Test case #%d\n",k);
  		
  		
  		
  		for(int i=1;i<=n;i++){
  			double x1,y1,x2,y2;
  			scanf("%lf %lf %lf %lf",&x1,&y1,&x2,&y2);
  				
  			p[i]={x1,y1,y2,1};
  			p[i+n]={x2,y1,y2,-1};
  			// 收集每个y坐标
  			raw[i]=y1;  
  			raw[i+n]=y2;
  		}
  		
  		n+=n;
  		
          // 对y坐标进行离散化
  		sort(raw+1,raw+1+n);
  		
  		int m=unique(raw+1,raw+1+n)-(raw+1);
          // 将每个y坐标映射到对于的一个唯一的数字
  		for(int i=1;i<=m;i++){
  			val[raw[i]]=i;
  		}
  		sort(p+1,p+1+n,cmp);
  		
          
          // 对y坐标建立线段树进行维护
  		build(1,1,m-1);
  		
  		double res=0;
  		for(int i=1;i<n;i++){
  			int y=val[p[i].y1],z=val[p[i].y2]-1;
  			change(1,y,z,p[i].flag);
              // 计算面积
  			res+=Node[1].len*(p[i+1].x-p[i].x);
  		}
  		
  		printf("Total explored area: %.2lf\n",res);
  		puts("");
  		
  	}
  	return 0;
  }
  ```

##### 主席树

+ 一半用来查询区间的第k大的值

```cpp
/* 
 * 给出一个序列，查询区间内有多少个不相同的数 
 */ 
//保存所有版本的线段树--主席树
const int MAXN=30010;
const int M=MAXN*100;
int a[MAXN];
template<typename Tp>
struct seg
{
	#define lson l,mid
	#define rson mid+1,r
	int tot;
	int T[MAXN];
	struct node
	{
		int ls,rs;
		Tp sum;
		void init()
		{
			sum=0;
		}
	}tr[M];
	void init()
	{
		tot=0;
	}
	int newnode()
	{
		return tot++;
	}
	int build(int l,int r)
	{
		int rt=newnode();
		tr[rt].init();
		if(l==r) return rt;
		int mid=(l+r)>>1;
		tr[rt].ls=build(lson);
		tr[rt].rs=build(rson);
		return rt;
	}
	int update(int pos,Tp val,int l,int r,int rt)
	{
		int nrt=newnode();
		tr[nrt].sum=tr[rt].sum+val;
		if(l==r) return nrt;
		int mid=(l+r)>>1;
		if(mid>=pos)
		{
			tr[nrt].rs=tr[rt].rs;
			tr[nrt].ls=update(pos,val,lson,tr[rt].ls);
		}
		if(mid<pos)
		{
			tr[nrt].ls=tr[rt].ls;
			tr[nrt].rs=update(pos,val,rson,tr[rt].rs);
		}
		return nrt;
	}
	Tp query(int L,int R,int l,int r,int rt)
	{
		if(L<=l&&r<=R) return tr[rt].sum;
		int mid=(l+r)>>1;
		Tp ret=0;
		if(L<=mid) ret+=query(L,R,lson,tr[rt].ls);
		if(mid<R) ret+=query(L,R,rson,tr[rt].rs);
		return ret;
	}
};
seg<int> se;
int main()
{
	//freopen("in.txt","r",stdin); 
	//freopen("out.txt","w",stdout); 
	while(scanf("%d",&n) == 1)
	{
		for(int i=1;i<=n;i++)
			scanf("%d",&a[i]);
		se.T[n+1]=se.build(1,n);
		map<int,int> mp;
		for(int i=n;i>=1;i--)
		{
			if(mp.find(a[i])==mp.end())
			{
				se.T[i]=se.update(i,1,1,n,se.T[i+1]);
			}
			else
			{
				int tmp=se.update(mp[a[i]],-1,1,n,se.T[i+1]);
				se.T[i]=se.update(i,1,1,n,tmp);
			}
			mp[a[i]]=i;
		}
		scanf("%d",&q);
		while(q--)
		{
			int l,r;
			scanf("%d%d",&l,&r);
			printf("%d\n",se.query(l,r,1,n,se.T[l]));
		}
	}
	return 0;
}

```



#### 分块

+ 分块可以很好的解决线段树或者树状数组的相关问题，但是这种算法需要找到一个很好的分块的方法，不然很容易造成时间复杂度过高等问题。

+ 一般的分块的方法是将整个区间分为$\sqrt{n}$块进行维护，需要查询的时候，我们可以将这部分的区间进行分块维护。

+ 分块模板

  ```c++
  #include<iostream>
  
  using namespace std;
  const int N=500010;
  int a[N],pos[N],L[N],R[N];
  
  int main(){
      int n;
      cin>>n;
      for(int i=1;i<=n;i++){
          cin>>a[i];
      }
      int t=sqrt(n);
      for(int i=1;i<=t;i++){
          L[i]=(i-1)*t+1;
          R[i]=i*t;
      }
     	if(R[i]<n){
          t++;
          L[t]=R[t-1]+1;
          R[t]=n;
      }
      
      // 对需要维护的进行预处理
      for(int i=1;i<=t;i++){
          for(int j=L[i];j<=R[i];j++){
              pos[j]=i;
              
              // 这里维护的是区间的和
              sum[i]+=a[j];
          }
      }
      
      
      // 进行后续的操作
      return 0;
  }
  ```

+ 
