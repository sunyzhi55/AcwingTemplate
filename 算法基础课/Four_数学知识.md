# AcWing——算法基础课


## 第四讲  数学知识

### 质数

#### 1.试除法判定质数

![image-20240208154627833](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240208154627833.png)

```c++
bool is_prime(int x)
{
    if (x < 2) return false;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
            return false;
    return true;
}
```



```c++
#include <iostream>
#include <algorithm>

using namespace std;

bool is_prime(int x)
{
    if (x < 2) return false;
    // x 的一个更小的因子为 i ，另一个更大的因子为 x / i ，遍历所有更小的因子
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
            return false;
            
    return true;
}

int main()
{
    int n;
    cin >> n;

    while (n -- )
    {
        int x;
        cin >> x;
        if (is_prime(x)) puts("Yes");
        else puts("No");
    }

    return 0;
}
```

#### 2.分解质因数

![image-20240208162404221](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240208162404221.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

void divide(int x)
{
	/*这里遍历的 i 肯定所有都是质数，假如 i 是合数那么 i 肯定也可分解为更小的质数相乘，由于循环是从小到大遍历的
	所以 i 分解的更小的质数肯定是遍历过， x 除过了的，所以不可能遍历到合数*/
	//因最多只有一个大于 sqrt(n) 的质因子，所以先遍历小于 sqrt(n) 的 
    for (int i = 2; i <= x / i; i ++ )
    	//每有一个质因子 
        if (x % i == 0)
        {
            int s = 0;
            while (x % i == 0) x /= i, s ++ ;
            cout << i << ' ' << s << endl;
        }
    //如果最后 x > 1 ，那此时的 x 就是那个大于 sqrt(n) 的质因子 
    if (x > 1) cout << x << ' ' << 1 << endl;
    cout << endl;
}

int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        int x;
        cin >> x;
        divide(x);
    }

    return 0;
}
```

```c++
void divide(int x)
{
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            int s = 0;
            while (x % i == 0) x /= i, s ++ ;
            cout << i << ' ' << s << endl;
        }
    if (x > 1) cout << x << ' ' << 1 << endl;
    cout << endl;
}
```



#### 3.筛质数

![image-20240208165154655](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240208165154655.png)

```c++

//埃氏筛法 

#include <iostream>
#include <algorithm>

using namespace std;

const int N= 1000010;

int primes[N], cnt;// primes 是存储质数的数组， cnt 是质数的数量 
bool st[N];

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ ){
        if(!st[i]){
        	primes[cnt ++ ] = i;
        	//把此质数的所有倍数筛掉 
        	for (int j = i + i; j <= n; j += i) st[j] = true;
		}
    }
}

int main()
{
    int n;
    cin >> n;

    get_primes(n);

    cout << cnt << endl;

    return 0;
}
```

```c++
int primes[N], cnt;// primes 是存储质数的数组， cnt 是质数的数量 
bool st[N];

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ ){
        if(!st[i]){
        	primes[cnt ++ ] = i;
        	//把此质数的所有倍数筛掉 
        	for (int j = i + i; j <= n; j += i) st[j] = true;
		}
    }
}
```



```c++

//线性筛法，数据大时会快很多
//详解参考：https://blog.csdn.net/littlegengjie/article/details/134164936

#include <iostream>
#include <algorithm>

using namespace std;

const int N= 1000010;

int primes[N], cnt;
bool st[N];//st[x]存储x是否被筛掉，非质数要被筛掉，起初全为 false ，全为质数

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        //当 i 是质数
        if (!st[i]) primes[cnt ++ ] = i;
        
        //在筛 i 的最小质因子的过程中把 primes[j] * i <= n 的所有合数筛出来 
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
        	/*
			当  i % primes[j] != 0  时：
			primes[j]一定小于 i 的最小质因子，而且 primes[j] 一定是primes[j] * i的最小质因子。
			把对应 primes[j] 的合数筛掉
			*/
            st[primes[j] * i] = true;
            /*
            当  i % primes[j] == 0  时：
			就说明枚举到 i 的最小质因子，退出循环 
			*/
            if (i % primes[j] == 0) break;
        }
    }
}

int main()
{
    int n;
    cin >> n;

    get_primes(n);

    cout << cnt << endl;

    return 0;
}

```

```c++
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}

```

### 约数

#### 1.试除法求约数

![image-20240217104638829](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240217104638829.png)

```c++
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

vector<int> get_divisors(int x)
{
    vector<int> res;
    //遍历更小的约数就行，更大的约数可以通过 x / i pushback进去 
    for (int i = 1; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res.push_back(i);
            //避免 x = i * i 时重复放入同一个数 
            if (i != x / i) res.push_back(x / i);
        }
    sort(res.begin(), res.end());
    return res;
}

int main()
{
    int n;
    cin >> n;

    while (n -- )
    {
        int x;
        cin >> x;
        vector<int> res = get_divisors(x);

        for (int x : res) cout << x << ' ';
        cout << endl;
    }

    return 0;
}
```

```c++
vector<int> get_divisors(int x)
{
    vector<int> res;
    for (int i = 1; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res.push_back(i);
            if (i != x / i) res.push_back(x / i);
        }
    sort(res.begin(), res.end());
    return res;
}
```

#### 2.约数个数

![image-20240217153842909](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240217153842909.png)

```c++

//约数个数定理：一个正整数的约数个数等于其质因子分解中每个质数指数加1的乘积。
/*
例如： 
将378000分解质因数378000=2^4×3^3×5^3×7^1
由约数个数定理可知378000共有正约数(4+1)×(3+1)×(3+1)×(1+1)=160个。
*/

#include <iostream>
#include <algorithm>
#include <unordered_map>
#include <vector>

using namespace std;

typedef long long LL;

const int N = 110, mod = 1e9 + 7;

int main()
{
    int n;
    cin >> n;

    unordered_map<int, int> primes;//primes[i] 表示 i 这个质因子对应的指数 

    while (n -- )
    {
        int x;
        cin >> x;
		//分解质因数
        for (int i = 2; i <= x / i; i ++ )
            while (x % i == 0)
            {
                x /= i;
                primes[i] ++ ;
            }
		//最多只有一个大于 sqrt(n) 的质因子，要考虑这个大于 sqrt(n) 的质因子
        if (x > 1) primes[x] ++ ;
    }

    LL res = 1;
    
    for (pair<int,int> p : primes) res = res * (p.second + 1) % mod;

    cout << res << endl;

    return 0;
}
```

#### 3.约数之和

![image-20240217162152651](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240217162152651.png)

```c++

/*
例： 
将 37800 分解质因数可得

360=2^4*3^3*5^3+7^1

1.由约数个数定理可知 378000 共有正约数(4+1)×(3+1)×(3+1)×(1+1)=160个。
2.由约数和定理可知， 378000 所有正约数的和为
(2^0+2^1+2^2+2^3+2^4)×(3^0+3^1+3^2+3^3)×(5^0+5^1+5^2+5^3) x (7^0+7^1)
=(1+2+4+8+16)(1+3+9+27)(1+5+25+125)(1+7)=31×40×156 x8 =1537920
*/

#include <iostream>
#include <algorithm>
#include <unordered_map>
#include <vector>

using namespace std;

typedef long long LL;

const int N = 110, mod = 1e9 + 7;

int main()
{
    int n;
    cin >> n;

    unordered_map<int, int> primes;

    while (n -- )
    {
        int x;
        cin >> x;
		//分解质因数
        for (int i = 2; i <= x / i; i ++ )
            while (x % i == 0)
            {
                x /= i;
                primes[i] ++ ;
            }

        if (x > 1) primes[x] ++ ;
    }

    LL res = 1;
    for (pair<int,int> p : primes)
    {
        LL a = p.first, b = p.second;
        LL t = 1;
        while (b -- ) t = (t * a + 1) % mod;//算出开头例子的括号项 
        res = res * t % mod;//把括号项乘起来 
    }

    cout << res << endl;

    return 0;
}
```

#### 4.最大公约数

![image-20240217165505252](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240217165505252.png)

```c++

//欧几里得算法（辗转相除法） 

#include <iostream>

using namespace std;

int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;//如果 b 不为0，则返回 gcd(b, a % b)，否则返回 a。
}

int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        int a, b;
        cin>>a>>b;
        cout<<gcd(a,b)<<endl;
    }

    return 0;
}
```

### 欧拉函数

#### 1.欧拉函数

![image-20240217171909347](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240217171909347.png)

```c++

//互质是公约数只有1的两个整数，叫做互质整数。

#include <iostream>

using namespace std;


int phi(int x)
{
    int res = x;//res初始值是x!!
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res = res / i * (i - 1);//相当于 N * (1 - 1 / p)，是为了防止小数 
            while (x % i == 0) x /= i;
        }
    if (x > 1) res = res / x * (x - 1);

    return res;
}


int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        int x;
        cin >> x;
        cout << phi(x) << endl;
    }

    return 0;
}

```

```c++
int phi(int x)
{
    int res = x;//res初始值是x
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res = res / i * (i - 1);//相当于 N * (1 - 1 / p)，是为了防止小数 
            while (x % i == 0) x /= i;
        }
    if (x > 1) res = res / x * (x - 1);

    return res;
}
```

#### 2.筛法求欧拉函数

![image-20240219110124715](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240219110124715.png)

```c++
#include <iostream>

using namespace std;

typedef long long LL;

const int N = 1000010;


int primes[N], cnt;
int euler[N];//存储元素的欧拉函数 
bool st[N];


void get_eulers(int n)
{
	// 1 的欧拉函数是 1  
    euler[1] = 1;
    
    //在线性筛法的过程中求出 1 到 n 的欧拉函数 
    for (int i = 2; i <= n; i ++ )
    {
    	//当 i 是质数 
        if (!st[i])
        {
            primes[cnt ++ ] = i;
            //质数的欧拉函数为 i - 1 
            euler[i] = i - 1;
        }
        
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
        	//筛掉对应primes[j]的合数 
            int t = primes[j] * i;
            st[t] = true;
            
            //当 i % primes[j] == 0 时，primes[j] 是 i 的最小质因子，也是 primes[j] * i 的最小质因子  
            if (i % primes[j] == 0)
            {
            	//修正 primes[j] * i 的欧拉函数 
                euler[t] = euler[i] * primes[j];
                break;
            }
            
            //当 i % primes[j] ！= 0 时，primes[j] 不是 i 的最小质因子，但是 primes[j] * i 的最小质因子
            //修正 primes[j] * i 的欧拉函数
            euler[t] = euler[i] * (primes[j] - 1);
        }
    }
}


int main()
{
    int n;
    cin >> n;

    get_eulers(n);

    LL res = 0;
    for (int i = 1; i <= n; i ++ ) res += euler[i];

    cout << res << endl;

    return 0;
}
```

```c++
int primes[N], cnt;     // primes[]存储所有素数
int euler[N];           // 存储每个数的欧拉函数
bool st[N];         // st[x]存储x是否被筛掉


void get_eulers(int n)
{
    euler[1] = 1;
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i])
        {
            primes[cnt ++ ] = i;
            euler[i] = i - 1;
        }
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            int t = primes[j] * i;
            st[t] = true;
            if (i % primes[j] == 0)
            {
                euler[t] = euler[i] * primes[j];
                break;
            }
            euler[t] = euler[i] * (primes[j] - 1);
        }
    }
}

```

### 快速幂

#### 1.快速幂

![image-20240219171741661](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240219171741661.png)

![image-20240219171755499](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240219171755499.png)

```c++
#include <iostream>

using namespace std;

typedef long long LL;


LL qmi(int a, int b, int p)
{
	//防止 p = 1 
    LL res = 1 % p;
    while (b)
    {
    	//如果当前 b 的最低位为 1 则把结果乘上当前底数 a ，为 0 就不用乘  
        if (b & 1) res = res * a % p;
        //每次都要更新底数，使 a 为 a 的平方 
        a = (LL)a * a % p;//强转其中一个操作数防止溢出
        //b右移一位
        b >>= 1;
    }
    return res;
}


int main()
{
    int n;
    cin>>n;
    while (n -- )
    {
        int a, b, p;
        cin>>a>>b>>p;
        cout<<qmi(a, b, p)<<endl;;
    }

    return 0;
}
```

```c++
LL qmi(int a, int b, int p)
{
    LL res = 1 % p;
    while (b)
    {
        if (b & 1) res = res * a % p;
        a = (LL)a * a % p;
        b >>= 1;
    }
    return res;
}
```

#### 2.快速幂求逆元

![image-20240219174812186](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240219174812186.png)

```c++
#include <iostream>

using namespace std;

typedef long long LL;


LL qmi(int a, int b, int p)
{
    LL res = 1 % p;
    while (b)
    {
        if (b & 1) res = res * a % p;
        a = (LL)a * a % p;
        b >>= 1;
    }
    return res;
}


int main()
{
    int n;
    cin>>n;
    while (n -- )
    {
        int a, p;
        cin>>a>>p;
        /*
        a 和 p 互质并且 p 是质数时 a 的逆元为 a^p-2 mod p
        因 p 为质数，因子只有 1 和 p，所以 a 和 p 存在除 1 以外的公因数即不互质的话，那个公因数只能是 p
        所以只需要判断 p 是否为 a 的因子就可以确定 a 和 p 是否互质
        */
        if (a % p == 0) puts("impossible");
        else cout<<qmi(a, p - 2, p)<<endl;
    }

    return 0;
}
```

### 扩展欧几里得算法

#### 1.扩展欧几里得算法

![image-20240220095303626](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220095303626.png)

![image-20240220100448292](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220100448292.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

int exgcd(int a, int b, int &x, int &y)//必须传引用，不能用全局变量
{
	//b 为 0 时，最大公约数为 a ，所以 a x 1 + 0 x 0 = a 
    if (!b)
    {
        x = 1, y = 0;
        return a;
    }
    //b 不为 0 时，详细过程见手动模拟 
    int d = exgcd(b, a % b, y, x);
    y -= a / b * x;//调用函数求得最大公约数之后发现 y 要减 a / b * x 才是 b 的真正的系数
    return d;
}

int main()
{
    int n;
    scanf("%d", &n);//数据范围比较大时用scanf会快很多

    while (n -- )
    {
        int a, b;
        scanf("%d%d", &a, &b);
        int x, y;
        exgcd(a, b, x, y);
        printf("%d %d\n", x, y);
    }

    return 0;
}

```

```c++
// 求x, y，使得ax + by = gcd(a, b)
int exgcd(int a, int b, int &x, int &y)
{
    if (!b)
    {
        x = 1; y = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y -= (a/b) * x;
    return d;
}
```

#### 2.线性同余方程

![image-20240220162334884](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220162334884.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;


int exgcd(int a, int b, int &x, int &y)
{
    if (!b)
    {
        x = 1, y = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}


int main()
{
    int n;
    scanf("%d", &n);
    while (n -- )
    {
        int a, b, m;
        scanf("%d%d%d", &a, &b, &m);

        int x, y;
        int d = exgcd(a, m, x, y);
        // b 不是 a 和 m 的最大公约数的倍数 ，无解 
        if (b % d) puts("impossible");
        //ax + by = gcd ,ax0 + by0 = b 解得 x0 = x * b / gcd , y0 = y * b / gcd（扩大 b / gcd 倍）
        /*
        由题可知 b 的范围几乎就是 int 的范围，所以要转成 LL ，防止x * b结果溢出，但是题中所说输出答案必须在 int 范围之内，所以用 %d 保证输出答案为 int（%lld 为 long long）
        */
        else printf("%d\n", (LL)x * b / d % m);
    }

    return 0;
}

```

### 中国剩余定理（*）

#### 表达整数的奇怪方式

![image-20240220165253585](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220165253585.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;


LL exgcd(LL a, LL b, LL &x, LL &y)
{
    if (!b)
    {
        x = 1, y = 0;
        return a;
    }

    LL d = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}


int main()
{
    int n;
    cin >> n;

    LL x = 0, m1, a1;
    cin >> m1 >> a1;
    for (int i = 0; i < n - 1; i ++ )
    {
        LL m2, a2;
        cin >> m2 >> a2;
        LL k1, k2;
        LL d = exgcd(m1, m2, k1, k2);
        if ((a2 - a1) % d)
        {
            x = -1;
            break;
        }

        k1 *= (a2 - a1) / d;
        k1 = (k1 % (m2/d) + m2/d) % (m2/d);

        x = k1 * m1 + a1;

        LL m = abs(m1 / d * m2);
        a1 = k1 * m1 + a1;
        m1 = m;
    }

    if (x != -1) x = (a1 % m1 + m1) % m1;

    cout << x << endl;

    return 0;
}

```

### 高斯消元（*）

#### 1.高斯消元解线性方程组

![image-20240220165811740](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220165811740.png)

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cmath>

using namespace std;

const int N = 110;
const double eps = 1e-8;

int n;
double a[N][N];

int gauss()  // 高斯消元，答案存于a[i][n]中，0 <= i < n
{
    int c, r;
    for (c = 0, r = 0; c < n; c ++ )
    {
        int t = r;
        for (int i = r; i < n; i ++ )  // 找绝对值最大的行
            if (fabs(a[i][c]) > fabs(a[t][c]))
                t = i;

        if (fabs(a[t][c]) < eps) continue;

        for (int i = c; i <= n; i ++ ) swap(a[t][i], a[r][i]);  // 将绝对值最大的行换到最顶端
        for (int i = n; i >= c; i -- ) a[r][i] /= a[r][c];  // 将当前行的首位变成1
        for (int i = r + 1; i < n; i ++ )  // 用当前行将下面所有的列消成0
            if (fabs(a[i][c]) > eps)
                for (int j = n; j >= c; j -- )
                    a[i][j] -= a[r][j] * a[i][c];

        r ++ ;
    }

    if (r < n)
    {
        for (int i = r; i < n; i ++ )
            if (fabs(a[i][n]) > eps)
                return 2; // 无解
        return 1; // 有无穷多组解
    }

    for (int i = n - 1; i >= 0; i -- )
        for (int j = i + 1; j < n; j ++ )
            a[i][n] -= a[i][j] * a[j][n];

    return 0; // 有唯一解
}


int main()
{
    scanf("%d", &n);
    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < n + 1; j ++ )
            scanf("%lf", &a[i][j]);

    int t = gauss();
    if (t == 2) puts("No solution");
    else if (t == 1) puts("Infinite group solutions");
    else
    {
        for (int i = 0; i < n; i ++ )
            printf("%.2lf\n", a[i][n]);
    }

    return 0;
}

```

#### 2.高斯消元解异或线性方程组

![image-20240220170138830](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240220170138830.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;


int n;
int a[N][N];


int gauss()
{
    int c, r;
    for (c = 0, r = 0; c < n; c ++ )
    {
        int t = r;
        for (int i = r; i < n; i ++ )
            if (a[i][c])
                t = i;

        if (!a[t][c]) continue;

        for (int i = c; i <= n; i ++ ) swap(a[r][i], a[t][i]);
        for (int i = r + 1; i < n; i ++ )
            if (a[i][c])
                for (int j = n; j >= c; j -- )
                    a[i][j] ^= a[r][j];

        r ++ ;
    }

    if (r < n)
    {
        for (int i = r; i < n; i ++ )
            if (a[i][n])
                return 2;
        return 1;
    }

    for (int i = n - 1; i >= 0; i -- )
        for (int j = i + 1; j < n; j ++ )
            a[i][n] ^= a[i][j] * a[j][n];

    return 0;
}


int main()
{
    cin >> n;

    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < n + 1; j ++ )
            cin >> a[i][j];

    int t = gauss();

    if (t == 0)
    {
        for (int i = 0; i < n; i ++ ) cout << a[i][n] << endl;
    }
    else if (t == 1) puts("Multiple sets of solutions");
    else puts("No solution");

    return 0;
}

```

### 求组合数

#### 1.求组合数 I

![image-20240221092834023](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221092834023.png)

![image-20240221093227486](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221093227486.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 2010, mod = 1e9 + 7;


int c[N][N];


void init()
{
    c[0][0] = 1;
    for (int i = 1; i < N; i ++ )
        for (int j = 0; j <= i; j ++ )
            if (!j) c[i][j] = 1;
            else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
}


int main()
{
    int n;

    init();

    scanf("%d", &n);

    while (n -- )
    {
        int a, b;
        scanf("%d%d", &a, &b);

        printf("%d\n", c[a][b]);
    }

    return 0;
}
```

#### 2.求组合数 II

![image-20240221095734729](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221095734729.png)

![image-20240221100955744](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221100955744.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;

const int N = 100010, mod = 1e9 + 7;

//fact 存的是元素的阶乘，infact 存的是元素的阶乘的逆元 
int fact[N], infact[N];


int qmi(int a, int k, int p)
{
    int res = 1;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}


int main()
{
	//先初始化 0 的阶乘和阶乘逆元
    fact[0] = infact[0] = 1;
    
    //初始化阶乘数组和阶乘逆元数组 
    for (int i = 1; i < N; i ++ )
    {
    	//fact[i] = fact[i - 1] * i
        fact[i] = (LL)fact[i - 1] * i % mod;
        //infact[i] = infact[i - 1] * i 模 mod 的逆元 
        infact[i] = (LL)infact[i - 1] * qmi(i, mod - 2, mod) % mod;
    }


    int n;
    scanf("%d", &n);
    while (n -- )
    {
        int a, b;
        scanf("%d%d", &a, &b);
        //fact[a] * infact[b] 会溢出 1e9 了 ，所以要转 LL 并且先模 mod 一下 ，公式见上图 
        printf("%d\n", (LL)fact[a] * infact[b] % mod * infact[a - b] % mod);
    }

    return 0;
}

```

```c++
int qmi(int a, int k, int p)    // 快速幂模板
{
    int res = 1;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}

// 预处理阶乘的余数和阶乘逆元的余数
fact[0] = infact[0] = 1;
for (int i = 1; i < N; i ++ )
{
    fact[i] = (LL)fact[i - 1] * i % mod;
    infact[i] = (LL)infact[i - 1] * qmi(i, mod - 2, mod) % mod;
}

```

#### 3.求组合数 III

![image-20240221104052669](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221104052669.png)

![image-20240221161028800](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221161028800.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;


int qmi(int a, int k, int p)
{
    int res = 1;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}


int C(int a, int b, int p)
{
    if (b > a) return 0;//注意 b > a 的情况

    int res = 1;
    for (int i = 1, j = a; i <= b; i ++, j -- )
    {
    	// a 到 a - b + 1 总共 a - b 个数 
        res = (LL)res * j % p;
        //再乘 1 到 b 的逆元也就是 b 的阶乘的逆元 
        res = (LL)res * qmi(i, p - 2, p) % p;
    }
    return res;
}


int lucas(LL a, LL b, int p)
{
	//递归终点 a < p && b < p
    if (a < p && b < p) return C(a, b, p);
    
    return (LL)lucas(a / p, b / p, p) * C(a % p, b % p, p) % p;
}


int main()
{
    int n;
    cin >> n;

    while (n -- )
    {
        LL a, b;//注意 a 和 b 的类型
        int p;
        cin >> a >> b >> p;
        cout << lucas(a, b, p) << endl;
    }

    return 0;
}

```

```c++

//若 p 是质数，C(n, m) = C(n / p, m / p) * C(n % p, m % p) (mod p)


int qmi(int a, int k, int p)
{
    int res = 1;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}


int C(int a, int b, int p)
{
    if (b > a) return 0;
    int res = 1;
    for (int i = 1, j = a; i <= b; i ++, j -- )
    {   	
        res = (LL)res * j % p;
        res = (LL)res * qmi(i, p - 2, p) % p;
    }
    return res;
}


int lucas(LL a, LL b, int p)
{
	
    if (a < p && b < p) return C(a, b, p);
    return (LL)lucas(a / p, b / p, p) * C(a % p, b % p, p) % p;
}
```

#### 4.求组合数 Ⅳ

![image-20240221170231204](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221170231204.png)

![image-20240221195419952](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240221195419952.png)



```c++
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;


const int N = 5010;

int primes[N], cnt;
int sum[N];
bool st[N];

//线性筛法筛质数
void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}


//返回 n 的阶乘中质因子 p 的次数 
int get(int n, int p)
{
    int res = 0;
    while (n)
    {
        res += n / p;
        n /= p;
    }
    return res;
}


vector<int> mul(vector<int> a, int b)
{
    vector<int> c;
    int t = 0;
 
    for (int i = 0; i < a.size(); i ++ )
    {
        t += a[i] * b;
        c.push_back(t % 10);
        t /= 10;
    }
    while (t)
    {
        c.push_back(t % 10);
        t /= 10;
    }
    return c;
}


int main()
{
    int a, b;
    cin >> a >> b;
    
	//筛出 1 到 a 的质数，因为 b 和 a - b 都比 a 小，所以 1 到 b 和 a - b的质数包含在 1 到 a 的质数中 
    get_primes(a);
    
	//求出1 到 a ，1 到 b 和 a - b每个质数的总次数 
    for (int i = 0; i < cnt; i ++ )
    {
        int p = primes[i];
        //C(a,b) 中 p 的总次数等于 a! 中 p 的次数减去 a - b! 中 p 的次数减去b! 中 p 的次数 
        sum[i] = get(a, p) - get(a - b, p) - get(b, p);
    }

    vector<int> res;
    res.push_back(1);
    
	//把每个质数乘起来，即上图最后一步的结果（分解质因数） 
    for (int i = 0; i < cnt; i ++ )
        for (int j = 0; j < sum[i]; j ++ )
            res = mul(res, primes[i]);
	
	//输出最终结果 
    for (int i = res.size() - 1; i >= 0; i -- ) printf("%d", res[i]);
    puts("");

    return 0;
}

```

```c++
当我们需要求出组合数的真实值，而非对某个数的余数时，分解质因数的方式比较好用：
    1. 筛法求出范围内的所有质数
    2. 通过 C(a, b) = a! / b! / (a - b)! 这个公式求出每个质因子的次数。 n! 中p的次数是 n / p + n / p^2 + n / p^3 + ...
    3. 用高精度乘法将所有质因子相乘

int primes[N], cnt;     // 存储所有质数
int sum[N];     // 存储每个质数的次数
bool st[N];     // 存储每个数是否已被筛掉


void get_primes(int n)      // 线性筛法求素数
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}


int get(int n, int p)       // 求n！中 p 的次数
{
    int res = 0;
    while (n)
    {
        res += n / p;
        n /= p;
    }
    return res;
}


vector<int> mul(vector<int> a, int b)       // 高精度乘低精度模板
{
    vector<int> c;
    int t = 0;
    for (int i = 0; i < a.size(); i ++ )
    {
        t += a[i] * b;
        c.push_back(t % 10);
        t /= 10;
    }

    while (t)
    {
        c.push_back(t % 10);
        t /= 10;
    }

    return c;
}

get_primes(a);  // 预处理范围内的所有质数

for (int i = 0; i < cnt; i ++ )     // 求每个质因数的次数
{
    int p = primes[i];
    sum[i] = get(a, p) - get(b, p) - get(a - b, p);
}

vector<int> res;
res.push_back(1);

for (int i = 0; i < cnt; i ++ )     // 用高精度乘法将所有质因子相乘
    for (int j = 0; j < sum[i]; j ++ )
        res = mul(res, primes[i]);

```

#### 5.满足条件的01序列

![image-20240222100819729](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222100819729.png)

![image-20240222102207006](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222102207006.png)

```c++

/*给定n个0和n个1，它们按照某种顺序排成长度为2n的序列
满足任意前缀中0的个数都不少于1的个数的序列的数量为： Cat(n) = C(2n, n) / (n + 1)*/


#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;

const int N = 100010, mod = 1e9 + 7;


int qmi(int a, int b, int p)
{
    int res = 1;
    while (b)
    {
        if (b & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        b >>= 1;
    }
    return res;
}

int C(int a,int b, int p){
	if(b > a) return 0;
	int res = 1;
	for(int i = 1, j = a; i <= b; i++,j--){
		res = (LL)res * j % p;
		res = (LL)res * qmi(i, p - 2, p) % p;
	}
	return res;
} 


int main()
{
    int n;
    cin >> n;

    cout<<(LL)C(2 * n, n, mod) * qmi(n + 1, mod - 2, mod) % mod<<endl;

    return 0;
}

```

### 容斥原理

#### 能被整除的数

![image-20240222111632771](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222111632771.png)

![image-20240222171707026](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222171707026.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;

const int N = 20;

int p[N];


int main()
{
    int n, m;
    cin >> n >> m;
	
	//用 p 数组存储 m 个质数 
    for (int i = 0; i < m; i ++ ) cin >> p[i];

    int res = 0;
    //外层循环遍历从1 到 1111...(m个1，2^m - 1)的每一个数字，每个数字代表上图中第二幅图等式右边的一个加项 
    for (int i = 1; i < 1 << m; i ++ )
    {
        int t = 1, s = 0;//t 为选中集合对应质数的乘积 ，s 为选中集合的数量
		 
        //内层循环，确定当前加项的具体形式，从最低位开始遍历当前数字的每一位，每个数字都当成有 m 位，看当前数字有多少个 1，确定当前项是哪个集合或哪几个集合的交集 
        for (int j = 0; j < m; j ++ )
            if (i >> j & 1)
            {
            	//若 t * p[j] > n ，则 n / t = 0，无意义 
                if ((LL)t * p[j] > n)
                {
                    t = -1;
                    break;
                }
                t *= p[j];//乘上集合对应的质数
                s ++ ;//有一个 1 ，集合数量加一 
            }

        if (t != -1)
        { 
            //除对应集合的质数的乘积就是集合的数量
            if (s % 2) res += n / t;//奇数项为正 
            else res -= n / t;//偶数项为负 
        }
    }

    cout << res << endl;

    return 0;
}
```

```c++
for (int i = 0; i < m; i ++ ) cin >> p[i];

    int res = 0;
    for (int i = 1; i < 1 << m; i ++ )
    {
        int t = 1, s = 0;
		 
        for (int j = 0; j < m; j ++ )
            if (i >> j & 1)
            {
                if ((LL)t * p[j] > n)
                {
                    t = -1;
                    break;
                }
                t *= p[j];
                s ++ ; 
            }

        if (t != -1)
        { 
            if (s % 2) res += n / t;
            else res -= n / t; 
        }
    }
```



### 博弈论

#### 1.Nim游戏

![image-20240222175803606](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222175803606.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;


int main()
{
    int n;
    scanf("%d", &n);

    int res = 0;
    while (n -- )
    {
        int x;
        scanf("%d", &x);
        res ^= x;
    }

    if (res) puts("Yes");
    else puts("No");

    return 0;
}
```

#### 2.台阶-Nim游戏

![image-20240222180647209](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240222180647209.png)

```c++

//奇数台阶上的值的异或值为非0，则先手必胜，反之必败！

#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010;

int main()
{
    int n;
    scanf("%d", &n);

    int res = 0;
    for (int i = 1; i <= n; i ++ )
    {
        int x;
        scanf("%d", &x);
        //奇数二进制形式最低位为 1 ，&1 后为 1，偶数为 0 
        if (i & 1) res ^= x;
    }

    if (res) puts("Yes");
    else puts("No");

    return 0;
}

```

#### 3.集合-Nim游戏

![image-20240223094335090](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240223094335090.png)

```c++
#include <cstring>
#include <iostream>
#include <algorithm>
#include <unordered_set>

using namespace std;

const int N = 110, M = 10010;

int n, k;
int s[N], f[M];//s 存储的是可拿取的石子数量，f 存储的是存储已经计算过的状态（石子数量）的 Nim 值，避免重复计算 


int sg(int x)
{
    if (f[x] != -1) return f[x];
    
	// S 要在函数内部定义 ，因为每次递归 S 都是针对当前状态的，S用于当前状态之后的状态的sg值 
    unordered_set<int> S;
    
    //递归计算当前状态 x 可以到达的之后的所有状态的值插入 S（x 能否减可拿取石子数量集合 S 中的元素） 
    for (int i = 0; i < k; i ++ )
    {
        int sum = s[i];
        if (x >= sum) S.insert(sg(x - sum));
    }
	
	//递归完之后就剩当前状态 x 没有值（不在 S 集合中）了，遍历自然数，如果有不在 S 中的自然数便是当前状态 x 的 sg 值 
    for (int i = 0; ; i ++ )
        if (!S.count(i))
            return f[x] = i;
}


int main()
{
    cin >> k;
    for (int i = 0; i < k; i ++ ) cin >> s[i];
    cin >> n;

    memset(f, -1, sizeof f);

    int res = 0;
    for (int i = 0; i < n; i ++ )
    {
        int x;
        cin >> x;
        res ^= sg(x);
    }

    if (res) puts("Yes");
    else puts("No");

    return 0;
}
```

#### 4.拆分-Nim游戏

![image-20240223103605920](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240223103605920.png)

![image-20240223103805594](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240223103805594.png)

```c++
#include <cstring>
#include <iostream>
#include <algorithm>
#include <unordered_set>

using namespace std;

const int N = 110;


int n;
int f[N];


int sg(int x)
{
    if (f[x] != -1) return f[x];

    unordered_set<int> S;
    
    //x > i >= j
    for (int i = 0; i < x; i ++ )
        for (int j = 0; j <= i; j ++ )
            S.insert(sg(i) ^ sg(j));

    for (int i = 0;; i ++ )
        if (!S.count(i))
            return f[x] = i;
}


int main()
{
    cin >> n;

    memset(f, -1, sizeof f);

    int res = 0;
    while (n -- )
    {
        int x;
        cin >> x;
        res ^= sg(x);
    }

    if (res) puts("Yes");
    else puts("No");

    return 0;
}
```

**NIM游戏 —— 模板题 AcWing 891. Nim游戏**
给定N堆物品，第i堆物品有Ai个。两名玩家轮流行动，每次可以任选一堆，取走任意多个物品，可把一堆取光，但不能不取。取走最后一件物品者获胜。两人都采取最优策略，问先手是否必胜。

我们把这种游戏称为NIM博弈。把游戏过程中面临的状态称为局面。整局游戏第一个行动的称为先手，第二个行动的称为后手。若在某一局面下无论采取何种行动，都会输掉游戏，则称该局面必败。
所谓采取最优策略是指，若在某一局面下存在某种行动，使得行动后对面面临必败局面，则优先采取该行动。同时，这样的局面被称为必胜。我们讨论的博弈问题一般都只考虑理想情况，即两人均无失误，都采取最优策略行动时游戏的结果。
NIM博弈不存在平局，只有先手必胜和先手必败两种情况。

定理： NIM博弈先手必胜，当且仅当 A1 ^ A2 ^ … ^ An != 0

**公平组合游戏ICG**
若一个游戏满足：

1. 由两名玩家交替行动；
2. 在游戏进程的任意时刻，可以执行的合法行动与轮到哪名玩家无关；
3. 不能行动的玩家判负；

则称该游戏为一个公平组合游戏。
NIM博弈属于公平组合游戏，但城建的棋类游戏，比如围棋，就不是公平组合游戏。因为围棋交战双方分别只能落黑子和白子，胜负判定也比较复杂，不满足条件2和条件3。

**有向图游戏**
给定一个有向无环图，图中有一个唯一的起点，在起点上放有一枚棋子。两名玩家交替地把这枚棋子沿有向边进行移动，每次可以移动一步，无法移动者判负。该游戏被称为有向图游戏。
任何一个公平组合游戏都可以转化为有向图游戏。具体方法是，把每个局面看成图中的一个节点，并且从每个局面向沿着合法行动能够到达的下一个局面连有向边。

**Mex运算**
设S表示一个非负整数集合。定义mex(S)为求出不属于集合S的最小非负整数的运算，即：
mex(S) = min{x}, x属于自然数，且x不属于S

**SG函数**
在有向图游戏中，对于每个节点x，设从x出发共有k条有向边，分别到达节点y1, y2, …, yk，定义SG(x)为x的后继节点y1, y2, …, yk 的SG函数值构成的集合再执行mex(S)运算的结果，即：
SG(x) = mex({SG(y1), SG(y2), …, SG(yk)})
特别地，整个有向图游戏G的SG函数值被定义为有向图游戏起点s的SG函数值，即SG(G) = SG(s)。

**有向图游戏的和 —— 模板题 AcWing 893. 集合-Nim游戏**
设G1, G2, …, Gm 是m个有向图游戏。定义有向图游戏G，它的行动规则是任选某个有向图游戏Gi，并在Gi上行动一步。G被称为有向图游戏G1, G2, …, Gm的和。
有向图游戏的和的SG函数值等于它包含的各个子游戏SG函数值的异或和，即：
SG(G) = SG(G1) ^ SG(G2) ^ … ^ SG(Gm)

**定理**
有向图游戏的某个局面必胜，当且仅当该局面对应节点的SG函数值大于0。
有向图游戏的某个局面必败，当且仅当该局面对应节点的SG函数值等于0。

## 第五讲  动态规划

### 背包问题

![image-20240228115320696](https://ye-hai.oss-cn-shenzhen.aliyuncs.com/typora/image-20240228115320696.png)

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int f[N];//f[j] 表示背包容量为 j 时能获得的最大价值。

int main()
{
    cin >> n >> m;
    
    while(n--){
    	int v, w;
		cin >> v >> w; 
		//逆序遍历（确保在更新 f[j] 的时候，使用的是上一轮迭代中的值 f[j - v[i]]） 
        for (int j = m; j >= v; j -- ){
        	//选择不放入当前物品和放入当前物品中的最大价值。
            f[j] = max(f[j], f[j - v] + w);
		}
	}
	

    cout << f[m] << endl;

    return 0;
}

```

