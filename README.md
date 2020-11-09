# common_computer

## 1. 개발환경
* django : 수를 입력받아 그 수의 소인수 중 가장 큰값을 return 해주는 web program 개발
* docker : 위에 django로 만들어진 web을 docker container에 담아 image로 만들었다.
* kubernetes : docker의 image를 배포하기위해 사용

## 2. 사용 알고리즘
* 폴라드로(Pollard-Rho)
* 밀러라빈(Miler-Rabin)
<br>
O(N^(1/4))에 소인수 분해를 할 수 있는 폴라드로 알고리즘을 사용하면 되는데 특정 소수에 대해서는 무한루프가 생긴다. <br>따라서 소수임을 O(k * log^3n) 다항 시간에 판별할 수 있는 밀러라빈 알고리즘을 함께 써야 한다.밀러라빈을 구현할 때 빠른 거듭제곱 알고리즘 사용한다

### 2.1 폴라드로(Pollard-Rho)

pollard-rho(ρ) 알고리즘은 지난 이산 로그 문제에서와 마찬가지로 일종의 충돌쌍을 이용해 소인수를 찾는 방식이다.<br> 구체적으로, 어떤 x,x′이 x≠x′, x≡x′(modp)을 만족한다면 gcd(x−x′,n) 은 최소한 p를 약수로 가진다는 사실을 이용한다.

### 2.2 밀러라빈(Miler-Rabin)
밀러-라빈 소수 판별법은 특정한 수 N이 소수인지 '확률적으로' 판단할 수 있는 판별법이다.'확률적으로' 라는 의미는, 밀러-라빈 소수 판별법을 이용했을 때, N이 '합성수'이거나, '아마도 소수일 것이다'라고 판단을 해준다는 것이다.  대신 우리가 생각하는 큰 수 (long long) 범위 내에서는 소수인지 정확히 판단이 가능하므로 너무 걱정할 필요는 없을 것 같다.

### 2.3 code
``` python


def input(request):

    val1 = int(request.GET['input'])

    n = val1

    def power(x, y, p):
        res = 1
        x = x % p
        while y > 0:
            if y & 1:
                res = (res * x) % p
            y = y >> 1
            x = (x*x) % p
        return res

    def gcd(a, b):
        if a < b:
            a, b = b, a
        while b != 0:
            r = a % b
            a = b
            b = r
        return a

    def miller_rabin(n, a):
            r = 0
            d = n-1
            while d % 2 == 0:
                r += 1
                d = d//2

            x = power(a, d, n)
            if x == 1 or x == n-1:
                return True

            for i in range(r-1):
                x = power(x, 2, n)
                if x == n - 1:
                    return True
            return False

    def is_prime(n):
        alist = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41]
        if n == 1:
            return False
        if n == 2 or n == 3:
            return True
        if n % 2 == 0:
            return False
        for a in alist:
            if n == a:
                return True
            if not miller_rabin(n, a):
                return False
        return True


    def pollardRho(n):
        if is_prime(n):
            return n
        if n == 1:
            return 1
        if n % 2 == 0:
            return 2
        x = random.randrange(2, n)
        y = x
        c = random.randrange(1, n)
        d = 1
        while d == 1:
            x = ((x ** 2 % n) + c + n) % n
            y = ((y ** 2 % n) + c + n) % n
            y = ((y ** 2 % n) + c + n) % n
            d = gcd(abs(x - y), n)
            if d == n:
                return pollardRho(n)
        if is_prime(d):
            return d
        else:
            return pollardRho(d)

    list = []
    while n > 1:
        divisor = pollardRho(n)
        list.append(divisor)
        n = n // divisor

    list.sort()

    res = list[-1]
    return render(request,'result.html',{'result':res})

```
