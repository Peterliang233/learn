# 数论

## 蜚蜀定理

+ 蜚蜀定理时关于最大公因数的定理，定义。

  若gcd(a,b)=d,那么对于任意的整数x,y,ax+by都一定时d的倍数，特别的，一定存在整数x,y，使得ax+by=d成立。

+ n个整数间的蜚蜀定理

  设a1,a2,a3...an为n个整数，**d时他们的最大公约数**，那么存在整数x1,x2,x3..xn使得x1a1+x2a2+..+xnan=d,特殊的，如果这n个数字都是互质的，那么就存在x1a1+x2a2+..+xnan=1.

  ```C++
  ll gcd(ll a,ll b){
      if(b==0) return a;
      return gcd(b,a%b);
  }
  ```
