### 字符串

#### 字符串哈希

+ 构造哈希函数

  + 哈希函数的一般构造方法：
    $$
    \sum^{l}_{i=1}{s[i]b^{l-i}}(modM)
    $$
    上述构造方法是通过给每个位置的字符增加一个权值，权值的大小根据字符串的位置，这样可以很大程度上避免冲突的产生。

  + 上述的M通常为一个素数，一般可以去233，2333，23333....，为了防止哈希冲突，可以使用多个不同的M；b可以使用任意的值，但是一般都是选择10的倍数。

+ 使用场景

  + 可以用于求某一个字符串的子串是否存在？这一场景一般可以利用KMP算法来进行实现，但是当我们的字符串的长度很大的时候，KMP算法容易超时。这样，我们就可以使用字符串哈希+二分的方法来进行求解。
  + 给出n个字符串，要求我们求这n个字符串的最长公共的子字符串。我们可以利用二分来求字符串的长度，然后对于每个长度，我们求每个字符该长度的子串，然后将其存入哈希表里面，然后求这n个哈希表的交集，这里取交集的方法我们可以用一个map来进行存储每个哈希值，同时用一个ans时刻更新map的最大值。
  + 求字符串的最长回文子串。我们首先对字符串进行预处理，然后进行枚举该最长回文子串的中心的坐标。

```C++
//回文串哈系模板,一个正哈系和一个反哈系，然后截取相同的进行一个比较就行了
typedef unsigned long long ll;
const int base1=233;
const int base2=23333;
ll power[1000010],has[1000010],has_reverse[1000010];
char s[1000010];
int main(){
    scanf("%s",s+1);
    int len=strlen(s+1);
    power[0]=1;
    has[0]=0,has_reverse[0]=0;
    for(int i=1;i<len+1;i++){
        power[i]=power[i-1]*base1;
    }
    for(int i=1;i<=len;i++){
        has[i]=has[i-1]*base1+s[i-1]-'a'+1;
        has_reaverse[i]=has_reverse[i-1]*base1+s[len-i+1]-a'+1;
    }
    int m;
    scanf("%d",&m);
    while(m--){
        int l,r;
        scanf("%d%d",&l,&r);
        ll tmp1=has[r]-has[l-1]*power[r-l+1];
        ll tmp2=has_reverse[len-i+1]-has_reverse[len-r]*power[r-l+1];
        if(tmp1==tmp2){
            puts("YES");
        }else{
            puts("NO");
        }
    }
    return 0;
}

```

#### 矩阵哈希

+ 对矩阵的哈希，其实可以利用一个二维前缀和

+ $$
  dp[i][j]=s[j]+dp[i-1][j]*base1+dp[i][j-1]*base2-dp[i-1][j-1]*base1*base2;
  $$

  ```C++
  //这是矩阵哈希模板
  typedef unsigned long long ll;
  const int maxn=100010;
  ll p1[maxn],p2[maxn],hash1[maxn],hash2[maxn],a[maxn][maxn];
  const ll pi=131,pj=233;
  void init(){
      p1[0]=1;
      p2[0]=1;
      for(int i=1;i<=10001;i++){
          p1[i]=p1[i-1]*233;
          p2[i]=p2[i-1]*131;
      }
  }
  
  void _hash(){
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              hash1[i][j]=hash1[i-1][j]*pi+a[i][j];
          }
      }
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              hash1[i][j]=hash1[i][j-1]*pj+a[i][j];
          }
      }
  }
  
  int main(){
      init();
      cin>>n>>m;
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              cin>>a[i][j];
          }
      }
      _hash();
      //如果想要或得一个(a,b)大小的矩阵，我们可以
      for(int i=a;i<=n;i++){
          for(int j=b;j<=m;j++){
              ll has=hash1[i][j];
              has-=hash1[i-a][j]*p2[a];
              has-=hash1[i][j-b]*p1[b];
              has+=hash1[i-a][j-b]*p2[a]*p1[b];
              //这样，我们就可以求出每一个大小ab的矩阵的哈希值了
          }
      }
  }
  ```

#### KMP算法

+ next数组

  对于一个字符串，它的每个前缀的next数组就是这个前缀的最长前缀==最长后缀，例如：`abcdeabcd`，它的next数组的值就是4，因为最长相等的前后缀为`abcd`

+ 因此，当我们进行模式串匹配的时候，给出的一个文本串t，和一个模式串s，我们可以构造出一个字符串为s#t，这样我们可以通过KMP求出每个位置的next的值，然后对于每一个值，等于模式串长度的就存在它的子串。

+ next数组的长度不能为这个字符串的自身。

  ```C++
  string s;
  cin>>s;
  int n=s.size();
  for(int i=1;i<n;i++){
       int j=kmp[i-1];
       while(j>0&&s[i]!=s[j]) j=kmp[j-1];   //kmp[i]表示的是最长相等的前后缀的长度
       if(s[i]==s[j]) j++;
       kmp[i]=j;
  }
  ```

#### 扩展KMP算法

+ 扩展kmp算法是z[i]表示的是字符串s[i,i+1,i+2...n-1]与字符串s的最长公共前缀的长度。
+ 利用这个算法可以在线性时间内求出一个字符串在另一个字符串中出现的所有的位置。
+ 算法过程
  + 最与每个z[i]，我们确保了i之前的所有的z都已经求出来了。那么我们用[l,r]表示我们求到的目前的最长的公共前缀。如果i<r,那么z[i]=min(z[i-l],r-i+1)
  + 如果i>r,那么我们直接按照朴素的算法进行求解。
  + 最后，如果z[i]>r，我们可以更新l,r

```C++
vector<int> z_function(string s) {
  int n = (int)s.length();
  vector<int> z(n);
  for (int i = 1, l = 0, r = 0; i < n; ++i) {
    if (i <= r && z[i - l] < r - i + 1) {
      z[i] = z[i - l];
    } else {
      z[i] = max(0, r - i + 1);
      while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
    }
    if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
  }
  return z;
}
```

#### 字典树

+ ```C++
  //以0为根节点
  struct trie{
      int nxt[100010][26],cnt;
      bool exist[100010];  //该结点结尾的字符串是否存在
      void insert(char *s,int l){  //插入字符串，l为字符串的长度
          int p=0;
          for(int i=0;i<l;i++){
              int c=s[i]-'a';
              if(!nxt[p][c]) nxt[p][c]=++cnt;
              p=nxt[p][c];
          }
          exist[p]=1;
      }
  
      bool find(char *s,int l){  //l为字符串的长度
          int p=0;
          for(int i=0;i<l;i++){
              int c=s[i]-'a';
              if(!nxt[p][c]) return 0;
              p=nxt[p][c];
          }
          return exist[p];
      }
  }
  ```

+ 模板

  ```C++
  #include<iostream>
  #include<algorithm>
  #include<cstring>
  #include<vector>
  #include<map>
  #include<stack>
  #include<queue>
  #include<fstream>
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  const int mod = 1e9+7;
  struct node{
      int nxt[2];
      int end,sum;
  }Node[500010];
  int a[100010];
  int main(){
    //  freopen("test.in","r",stdin);
      int n,m,js=1;
      memset(Node,0,sizeof(Node));
      cin>>m>>n;
      for(int i=1;i<=m;i++){
          int len;
          cin>>len;
          for(int j=1;j<=len;j++){
              cin>>a[j];
          }
          int now=1;
          for(int j=1;j<=len;j++){
              if(Node[now].nxt[a[j]]!=0) now=Node[now].nxt[a[j]];
              else{
                  js++;
                  Node[now].nxt[a[j]]=js;
                  Node[js].nxt[1]=Node[js].nxt[0]=0;
                  now=js;
              }
              Node[now].sum++;
          }
          Node[now].end++;
      }
      for(int i=1;i<=n;i++){
          int len;
          cin>>len;
          for(int j=1;j<=len;j++){
              cin>>a[j];
          }
          int now=1,ans=0;
          bool flag=true;
          for(int j=1;j<=len;j++){
              if(Node[now].nxt[a[j]]!=0) now=Node[now].nxt[a[j]];
              else{
                  flag=false;
                  break;
              }
              ans+=Node[now].end;
          }
          if(!flag) cout<<ans<<endl;
          else cout<<ans+Node[now].sum-Node[now].end<<endl;
      }
      fclose(stdout);
      return 0;
  }
  ```

+ 字典树就是我们把一个字符串构建成一颗树的形式，我们可以对每一个节点进行标号，然后我们可以从根节点进行便利过去，如果遇到某一个节点的下一个不存在的话，那么就构建一个新的节点出来。最后我们所有的字符串就构建成了一棵树的形式，我们同时可以给每个节点加上一些其他的属性，比如表示经过这个节点的字符串的个数或者是以这个节点结束的字符的个数等等。

#### Manacher算法

+ 马拉车算法是用来求最大的回文字符串的长度的一个算法，它的时间复杂度为O(n).这种算法是采用一边优化，一边暴力的算法进行求解的。核心思想是每次记录下当前的最长的回文串的最左边和最右边的长度，然后，我们枚举每个中心，通过与左右边界的位置来进行判断它的边界。这里奇数和偶数是分开来计算的。我们可以在现在原字符串的左右两边插入一个肯定不会出现的字符，然后在每个字符中间插入一个‘#’,最后我们只需要按奇数长度的字符来进行处理就行了。

+ 计算最长回文半径的模板

  ```C++
  scanf("%s",c);
  int len=strlen(c);
  int cnt=0;
  s[++cnt]='^',s[++cnt]='#';
  for(int i=0;i<len;i++){
  	s[++cnt]=c[i];
  	s[++cnt]='#';
  }
  s[++cnt]='!';
  int mx=0;
  int id=0;
  for(int i=1;i<=cnt;i++){
  	if(i<mx) p[i]=min(p[id*2-i],mx-i);
  	else p[i]=1;
  	while(s[i-p[i]]==s[i+p[i]]) p[i]++;
  	if(mx<i+p[i]) id=i,mx=i+p[i];
  }
  ```

+ 计算最长回文长度的模板

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int d[23000000];
  int main(){
  	string s;
  	cin>>s;
  	int len=s.size();
  	string c="";
  	for(int i=0;i<len;i++){
  		c+="#";
  		c+=s[i];
  	}
  	c+="#";
  	len=c.size();
  	int ans=0;
  	for(int i=0,l=0,r=-1;i<len;i++){
  		int k=(i>r)?1:min(d[l+r-i],r-i);
  		while(0<=i-k&&i+k<len&&c[i-k]==c[i+k]){
  			k++;
  		}
  		d[i]=k--;
  		ans=max(ans,d[i]-1);
  		if(i+k>r){
  			l=i-k;
  			r=i+k;
  		}
  	}
  	cout<<ans;
  	return 0;
  }
  ```


#### ac自动机

+ ac自动机是KMP+Tire两种算法的综合，它解决的问题是如何查询一个文本串可以与多少个模式串的匹配。

+ ac自动机是将所有的模式串构造成一棵Trie，我们称之为Trie树。

+ 同时，我们要引入一个失配指针fail的概念，失配指针是指一个字符串中匹配当前状态的最长后缀。可以将它和KMP算法的next数组比较起来理解。fail指针就是一个模式串的后缀集合

+ ac自动机主要有两个方面，一个是构建fail指针，另一个就是构建自动机。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+6;  //字符串的长度
  int n;
  int tr[N][26],tot;
  int e[N],fail[N];
  void insert(char *s){
      int u=0;
      for(int i=1;s[i];i++){
          if(!tr[u][s[i]-'a']) tr[u][s[i]-'a']=++tot;
          u=tr[u][s[i]-'a'];
      }
      e[u]++;
  }
  queue<int> q;
  void build(){
      for(int i=0;i<26;i++){
          if(tr[0][i]){
              q.push(tr[0][i]);
          }
      }
      while(q.size()){
          int u=q.front();
          q.pop();
          for(int i=0;i<26;i++){
              if(tr[u][i]){
                  fail[tr[u][i]]=tr[fail[u]][i];
                  q.push(tr[u][i]);
              }else{
                  tr[u][i]=tr[fail[u]][i];
              }
          }
      }
  }
  //进行询问的文本串
  int query(char *t){
      int u=0,res=0;
      for(int i=1;t[i];i++){
          u=tr[u][t[i]-'a'];
          for(int j=u;j&&e[j]!=-1;j=fail[j]){
              res+=e[j];
              e[j]=-1;
          }
      }
      return res;
  }
  char s[N];
  int main(){
      //freopen("test.in","r",stdin);
      scanf("%d",&n);
      for(int i=1;i<=n;i++){
          scanf("%s",s+1);
          insert(s);
      }
      scanf("%s",s+1);
      build();
      printf("%d\n",query(s));
      fclose(stdout);
      return 0;
  }
  ```

#####  常用模板

+ 这里存一个模板，对于n个字符串，求在母串里面出现的次数，这个模板包括相交的和不相交的，询问0表示相交的，询问1表示不相交的子串的个数。用ac自动机可以在很好的时间复杂度内解决这个问题。


  ```C++
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <queue>
  using namespace std;
  const int N = 800000;
  int Next[N][26], fail[N], val[N], sz, n, op[N], pos[N], last[N], res[N][2];
  char str[10], str2[N];
  
  void init() {
      memset(Next[0], 0, sizeof(Next[0]));
      val[0] = 0;
      sz = 1;
  }
  
  int insert(char *s) {
      int u = 0, len = strlen(s);
      for (int i = 0; i < len; i++) {
          int k = s[i] - 'a';
          if (!Next[u][k]) {
              memset(Next[sz], 0, sizeof(Next[sz]));
              val[sz] = i + 1;
              Next[u][k] = sz++;
          }
          u = Next[u][k];
      }
      return u;
  }
  
  void getFail() {
      queue<int> Q;
      fail[0] = 0;
      for (int i = 0; i < 26; i++)
          if (Next[0][i]) {
              fail[Next[0][i]] = 0;
              Q.push(Next[0][i]);
          }
      while (!Q.empty()) {
          int u = Q.front();
          Q.pop();
          for (int i = 0; i < 26; i++) {
              if (!Next[u][i])
                  Next[u][i] = Next[fail[u]][i];
              else {
                  fail[Next[u][i]] = Next[fail[u]][i];
                  Q.push(Next[u][i]);
              }
          }
      }
  }
  
  void query(char *s) {
      int u = 0, len = strlen(s);
      for (int i = 0; i < len; i++) {
          u = Next[u][s[i] - 'a'];
          int temp = u;
          while (temp != 0) {
              res[temp][0]++;
              if (i - last[temp] >= val[temp]) {
                  res[temp][1]++; 
                  last[temp] = i;
              }
              temp = fail[temp];
          }
      }
  }
  
  int main() {
      int cas = 1;
      while (scanf("%s", str2) == 1) {
          init();
          memset(res, 0, sizeof(res));
          memset(last, -1, sizeof(last));
          scanf("%d", &n);
          printf("Case %d\n", cas++);
          for (int i = 0; i < n; i++) {
              // 输入n个要询问的字符串
              scanf("%d%s", &op[i], str);
              pos[i] = insert(str);
          }
          // 构建一个ac自动机
          getFail();
          // 输入一个母串
          query(str2);
          for (int i = 0; i < n; i++)
              printf("%d\n", res[pos[i]][op[i]]);
          printf("\n");
      }
      return 0;
  }
  ```

  

#### 高精度

+ 下面主要是用C++模拟的一些大数加法，减法，乘法和除法

  ```C++
  
  vector<int> add(vector<int> &A,vector<int> &B){
      vector<int> C;
      int t=0;
      for(int i=0;i<A.size()||i<B.size();i++){
          if(i<A.size())  t+=A[i];
          if(i<B.size())  t+=B[i];
          C.push_back(t%10);
          t/=10;
      }
      if(t)   C.push_back(t);
      return C;
  }
  
  vector<int> mul(vector<int> &A,int b){
      vector<int> C;
      int t=0;
      for(int i=0;i<A.size()||t;i++){
          if(i<A.size())  t+=A[i]*b;//模拟
          C.push_back(t%10);
          t/=10;
      }
      while(C.size()>1&&C.back()==0)  C.pop_back();//排除前导0,b为0时会出现多余前导0
      return C;
  }
  
  vector<int> div(vector<int> &A,int b,int &r){
      vector<int> C;
      for(int i=A.size()-1;i>=0;i--){
          r=r*10+A[i];
          C.push_back(r/b);
          r%=b;
      }
      reverse(C.begin(),C.end());//翻转是为了方便去除前导0
      while(C.size()>1&&C.back()==0)  C.pop_back();
      return C;
  }
  void print(vector<int> a){
  	for(int i=a.size()-1;i>=0;i--){
  		cout<<a[i];
  	}
  	cout<<endl;
  }
  ```
