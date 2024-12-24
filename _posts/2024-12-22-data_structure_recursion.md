---
title: "(작성중)Data Structure : Recursion | 재귀"
author: 'philotti'
date: 2024-12-13 12:39:00 +0900
last_modified_at: 2024-12-14 17:40:00 +0900
categories: [Data Structure, Recursion]
tags: [Data Structure, Recursion, C++]
description: Recursion
toc: true 
math: true
pin: false
published: true
image:
  path: /assets/post_img/2024-12-13/recursion_logo.webp
  alt: Recursion
---

> 재귀의 개념과 장단점, 다양한 재귀의 종류를 유명한 재귀 예시들과 함께 소개하겠습니다.
{: .prompt-info }

---

재귀라는 단어 자체는 많이 들어보셨을 거라고 생각합니다. 다양한 분야에서도 비슷하게나마 쓰이는 개념이기 때문이라고 생각합니다. 물론, 프로그래밍 분야에서도 재귀라는 개념을 심심찮게 볼 수 있습니다. 실제로 유명한 수학적 개념 등을 프로그래밍 할 때 쓰이고 있고 다양한 장단점이 존재하므로 꼭 공부하고 지나가는 편이 좋습니다. 필자의 사용언어는 C++이며, Vscode를 이용하여 진행합니다.

<br>

## **Introduction**
- 일단 `재귀`가 무엇인지를 짚고 시작해야 합니다. 그래야 `다양한 관계` 등을 `이해`할 수 있기 때문이죠.
- 다양한 형태로 호출과 반환이 이루어지는데 이에 관한 `스택과 재귀의 관계`도 존재합니다.
- `시간 복잡성`이 무엇인지와 `구하는 방법`, `재귀와의 관계`에 대해서도 알아볼 것입니다.
- 재귀도 한 종류만 있지는 않습니다. `다양한 재귀의 종류`와 각각의 `기능`에 관해서도 이야기해보겠습니다.

---

## **Recursion**
재귀의 정의



### **<u>재귀의 종류</u>**

#### **꼬리 재귀(Tail Recursion)**
- 함수의 마지막 부분에서 순환하고 그 후에 바로 함수를 종료하는 재귀입니다. 즉, 모든 작업이 **호출 시간에만 수행**하고 함수 반환 시간에는 어떤 작업도 수행하지 않습니다. 
- 꼬리 재귀는 쉽게 `루프`로 전환할 수 있습니다. 
- 그러나 꼬리 재귀를 쓰는 것보다는 **루프를 쓰는 편이 공간 측면에서는 더 효율적**입니다. 그 이유는 꼬리 재귀를 사용하면 시간 복잡도가 `O(n)`이지만, 일반 루프를 사용하면 시간 복잡도가 `O(1)`이 되기 때문입니다.

```cpp
#include <iostream>
using namespace std;

void func(int n)
{
    if(n>0)
    {
        cout<<n<<" ";
        func(n-1);
    }
}

int main()
{
    func(3);
    return 0;
}
```
> Preview : 3 2 1 

<br>

#### **머리 재귀(Head Recursion)**
- 자신을 호출하는 구문이 가장 앞에 있는 재귀입니다. 즉, 모든 과정이 재귀 후에 이루어집니다. 
- 꼬리 재귀와는 다르게 루프로 변환하기 위해서는 함수 내용이 바뀌므로 쉽지 않습니다.
```cpp
#include <iostream>
using namespace std;

void func(int n)
{
	if (n > 0) {
		func(n - 1);
		cout<<" "<<n;
	}
}

int main()
{
	int x = 3;
	func(x);
	return 0;
}
```
> Preview : 1 2 3

<br>

#### **트리 재귀(Tree Recursion)**
- 어떠한 함수 내에서 자기 자신을 2번 이상 호출하는것을 트리 재귀라고합니다.
```cpp
void func(int n)
{
    if(n>0)
    {
        cout<<n<<" ";
        func(n-1);
        func(n-1);
    }
}

int main()
{
    func(3);
    return 0;
}
```
> Preview : 3 2 1 1 2 1 1

- 위의 시간복잡도는 O(2^n)이고, 프로그램 실행에 얼마나 많은 메모리가 필요한지를 뜻하는 공간 복잡도는 O(n)입니다.

<br>

#### **간접 재귀(Indirect Recursion)**
- 함수가 여러 개일 수 있습니다. 순환적으로 서로를 호출하죠. 예로 첫번째 함수는 두번째 함수를 호출하고, 두번째 함수는 세번째 함수를 호출하고, 세번째 함수는 첫번째 함수를 호출하는 순환함수 느낌이납니다. 이런 서로를 간접적으로 호출하는 함수를 간접재귀라고 합니다.

```cpp
#include <iostream>
using namespace std;
void funcB(int n);

void funcA(int n)
{
    if(n>0)
    {
        cout<<n<<" ";
        funcB(n-1);
    }
}

void funcB(int n)
{
    if(n>1)
    {
        cout<<n<<" ";
        funcA(n/2);
    }
}

int main()
{
    funcA(20);
    return 0;
}
```
> Preview : 20 19 9 8 4 3 1 

<br>

#### **중첩 재귀(Nested Recursion)**
- 말 그대로 재귀 안에 재귀가 들어가 호출하고 결과값이 도출되는 재귀입니다.

```cpp
#include <iostream>
using namespace std;

int fun(int n)
{
    if(n>100)
        return n-10;
    else
        return fun(fun(n+11));
}

int main()
{
    int r;
    r=fun(95);
    cout<<r<<" ";
    return 0;
}
```
> Preview : 91 

<br>

### **<u>Examples Of Recursion</u>**

#### **Example 1 : 팩토리얼**
- 위에서 언급했듯이 재귀함수는 함수가 `끝나는 지점`이 존재해야 합니다. 따라서 n 팩토리얼의 값이 0이 되면 1로 리턴하여 종료될 것입니다.
- 만약 5 팩토리얼을 구한다고 한다면 함수를 실행하면서 factorial(5)에서 차례로 factorial(0)까지 진행될 것입니다. 그 후 계산하지 않고 진행된 구문을 실행하게됩니다. 즉, `factorial(5)=factoral(4)*5` -> `factoral(5)=factoral(3)*5*4` -> `factoral(5)=factoral(2)*5*4*3` -> `factoral(5)=factoral(1)*5*4*3*2` -> `factoral(5)=factoral(0)*5*4*3*2*1`처럼 진행되었다가 계산하지 않은 구문은 거꾸로 진행하면서 `factoral(0)=1` -> `factoral(1)=1*1` -> `factoral(2)=1*1*2....` 와 같은 방식으로 결과인 120이 나오게됩니다.

```cpp
#include <iostream>
using namespace std;

int factorial(int n)
{
    if(n==0)
        return 1;
    return factorial(n-1)*n;
}

int main()
{
    int r=factorial(5);
    cout<<r<<endl;
    return 0;
}
```
> Preview : 120

<br>

#### **Example 2 : 지수**
- `math` 라이브러리를 이용하면 `pow` 함수를 이용하여 지수 곱을 사용할 수 있지만, 아래처럼 거듭 곱셈 형태로 재귀를 이용하여 지수를 구할 수 있습니다.

```cpp
#include <iostream>
using namespace std;

int power(int m,int n)
{
    if(n==0)
        return 1;
    return power(m,n-1)*m;
}

int main()
{
    int r=power(9,3);
    cout<<r<<endl;

    return 0;
}
```
> Preview : 729

<br>

#### **Example 3 : [테일러 급수](https://ko.wikipedia.org/wiki/%ED%85%8C%EC%9D%BC%EB%9F%AC_%EA%B8%89%EC%88%98)**
- `테일러 급수`는 매클로린 급수의 한 종류입니다. 미적분학을 배우신 분들이라면 매우 친숙할 급수입니다. 테일러 급수로 극한을 이용해 근사치를 구할 수 있기 때문이죠. 테일러 급수도 재귀적 성질을 가지고 있습니다.
- 재귀함수의 종료지점은 자연 상수의 지수가 0이 된다면 값을 1로 리턴할 것입니다.
- 자연 상수 `테일러 급수`는 분모는 1씩 증가하는 팩토리얼로 이루어져 있고 분자는 x의 지수가 증가하는 형태이므로 원하는 지수 값을 n으로 받아서 n이 0이 될 때까지 `재귀`를 이용해 아래의 함수를 이용하여 값을 더해준다면 쉽게 구할 수 있습니다. ~~math 라이브러리를 안쓰고...~~

```cpp
#include <iostream>
using namespace std;

double e(int x, int n)
{
    static double p=1,f=1;
    double r;
    if(n==0)
        return 1;
    r=e(x,n-1);
    p=p*x;
    f=f*n;
    return r+p/f;
}

int main()
{
    cout<<e(4,15)<<endl;
    return 0;
}
```
> Preview : 54.5979

<br>

#### **Example 4 : 테일러 급수(호너의 법칙)**

> 소수점이 많은 값을 출력하는 함수를 코드로 이용하여 구할 때에는 이 코드처럼 원하는 값이 정확하게 나오지 않을 수 있습니다. 따라서 float보다는 double과 같은 데이터 타입을 사용하면 오차를 줄일 수 있습니다.
{: .prompt-tip }

- [호너의 법칙](https://ko.wikipedia.org/wiki/%ED%98%B8%EB%84%88%EC%9D%98_%EB%B0%A9%EB%B2%95)은 쉽게 설명하자면 다항식을 푸는데 계산량을 줄일 수 있는 방법입니다.
- 아마 여러분도 일상이나 수학문제를 풀 때 사용했던 방법일 수도 있습니다. 자신이 눈치를 못 챌 뿐이죠. ~~저도 그랬거든요...~~
- 아래의 코드는 자연 상수 e를 구현한 코드입니다. 다른 테일러 급수인 sin, cos 등과 같은 삼각함수를 구현하신다면 아마 수가 커져서 `오버플로우`가 나타나므로 주의하여야 합니다.
- 위의 3번 방법보다 호너의 법칙을 적용한 코드를 쓰신다면 `시간복잡도`를 `O(n^2)`에서 `O(n)`로 줄여서 효율적으로 구현할 수 있다는 `장점`이 존재합니다.

```cpp
#include <iostream>
using namespace std;

double e(int x, int n)
{
    static double s;
    if(n==0)
        return s;
    s=1+x*s/n;
    return e(x,n-1);

}
int main()
{
    cout<<e(4,15)<<endl;
    return 0;
}
```
> Preview : 54.5971

<br>

#### **Example 5: 피보나치 수열**
```cpp
#include <stdio.h>

int fib(int n)
{
    int t0=0,t1=1,s=0,i;
    if(n<=1) return n;
    for(i=2;i<=n;i++)
    {
        s=t0+t1;
        t0=t1;
        t1=s;
    }
    return s;
}

int rfib(int n)
{
    if(n<=1)return n;
    return rfib(n-2)+rfib(n-1);
}

int F[10];

int mfib(int n)
{
    if(n<=1)
    {
        F[n]=n;
        return n;
    }
    else
    {
        if(F[n-2]==-1)
            F[n-2]=mfib(n-2);
        if(F[n-1]==-1)
            F[n-1]=mfib(n-1);
F[n]=F[n-2]+F[n-1];
return F[n-2]+F[n-1];
}
}
int main()
{
int i;
for(i=0;i<10;i++)
F[i]=-1;
printf("%d \n",mfib(5));
return 0;
```