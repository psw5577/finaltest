## 모의 담금질 기법

모의 담금질 기법은 광대한 탐색 공간 안에서, 주어진 함수의 전역 최적해에 대한 근사를 주는 문제풀이 방법 알고리즘이다. ---위키백과

모의 담금질 기법은 액체화된 높은 온도의 금속을 냉각시키면서 금속을 더 강하게 하는 방식인 담금질을 모방하여 해를 구하는 탐색 알고리즘이다.

모의 담금질 기법과 담금질의 공통점은, 처음에는 무작위로 특정한 패턴 없이 행해지지만 온도가 낮아짐에 따라 점차 규칙적으로 탐색/결정화 를 이루게 된다는 점이다. 이것을 이용해 모의 담금질이라는 것을 이해해 보면, 모의 담금질은 방식 자체는 규칙적인 알고리즘(예를 들어 0에서부터 10000까지의 범위 내에서 근을 찾는 상황에서 x 또는 t 등의 변수를 0부터 1씩 증가시켜가며 해를 찾는 방식)보다 처음엔 난잡한 방식으로 이루어지는 것으로 보일 수 있으나, 운이 좋게 특정 값을 찾았을 때 규칙적이고 빠른 방식으로 해를 찾아나간다는 점에서, 더 넓은 범위의 탐색 공간을 가지는 문제에서 더욱 효율적이라고 볼 수 있을 것 같다.

3차함수에서 최적해를 2개 찾을 수 있는데, 이 때 담금질 기준으로 더 낮은 값이 전역 최적해가 되고 더 높은 값이 지역 최적해가 된다. 모의 담금질을 시행할 때, 지역 최적해를 찾게 된다면 확률적으로 전역 최적해에 최대한 가깝게 다가가지만, 전역 최적해를 반드시 찾는다는 보장을 바랄 수는 없다.

https://sens.tistory.com/404
이 링크에 있는 움짤을 보면 이해를 도울 수 있다. 가장 높은 산봉우리를 찾는데 매번 해발고도 0m에서부터 출발하는 것과 중간지점에서 다시 올라가는 것은 효율이 다르다.  이렇듯 이전에 구한 해와 새로 구한 해를 비교해서 중간지점을 찾아 해까지 도달한다면 단순히 규칙적으로 해를 찾는 것보다 더 효율적일 수 있겠다.

한 가지 문제점이 있다면, 넓은 범위에서 해를 구하는 것에 능한 것이지, 코드가 해를 빨리 찾는 것은 아니다.  풀이 방법이 이미 널리 알려진 문제의 경우에는 당연히 해를 구하는데 훨씬 오래 걸리고, 그게 아니어도 근사로 구하는 방식들 처럼 정확한 값이 쉽게 나오지 않는 문제를 제외하면 대부분의 다른 알고리즘이 해를 명확하고 더 빠르게 구할 수 있을 것이다. 풀기 힘든 문제에 적합한 알고리즘이 아닌가 싶다.

## 코드

강의 때 따라했었지만 어딘가 잘못됐는지 코드는 동작하지 않았다. 이후 고치려고 해 봤지만 잘 되지 않았다.

```
public class SA{
	private double t;
	private double a; 
	private int niter;
	public ArrayList<Double> hist;
}
public SA(int niter){
	this.niter = niter;
	hist = new ArrayList<>();
}
    public double solve(Problem p, double t, double a, double lower, double upper){
        Random r = new Random();
        double x0 = r.nextDouble() * (upper - lower) + lower;
        return solve(p, t, a, x0, lower, upper);
        double f0 = p.fit(x0);
        for (int i=0; i < niter; i++){
            int kt = t;
            for(int j=0; j<kt; j++){
                double x1 = r.nextDouble() * (upper - lower) + lower;
                double f1 = p.fit(x1);
                if(p.isNeighborBetter(f0, f1)){
                    x0 = x1;
                    f0 = f1;
                    hist.add(f0);
                } else {
                    double d = Math.sqrt(Math.abs(f1 - f0));
                    double p0 = Math.exp(-d/t);
                    if(r.nextDouble() < 0.1) {
                        x0 = x1;
                        f0 = f1;
                        hist.add(f0);
                    }
                }
            }
            t *= a;
        }
        return x0;
    }
} 
```



```
package com.company;
public interface Problem{
	double fit(double x);
}
```



```
public class Main {
    public static void main(String[] args) {
        SA sa = new SA(100);
        com.company.Problem p = new com.company.Problem() {
            @Override
            public double fit(double x) {
                return x * x * x - 2 * x * x + x;/*x^3-2x^2+x, 해가 단순하게 나오게 하기 위해서 이중 근을 가지는 3차함수로 정했다. 하지만 해가 나오지 않았따.*/
            }
			double x = sa.solve(p, 100, -100, -50,  50);
            @Override
            public boolean isNeighborBetter(double f0, double f1) {
                return f0 > f1;
            }
        };
        System.out.println("x값" + x + "\n");
        System.out.println("y값" + p.fit(x) + "\n");
    }
}       
```

![gggg](https://user-images.githubusercontent.com/80510925/121703985-4c004980-cb0e-11eb-9a80-19544a902e9e.PNG)



/* 결과는 나오지 않아서 딱히 적을 수 있는 것이 없었고, 2번 문제 모델도 실행해 볼 수 없었다. 내가 생각해 둔 모델은 독립변수를 외식 횟수로, 종속 변수를 코로나 감염 가능성(%) 로 놓은 외식 횟수에 따른 코로나 감염 가능성이었는데, 아마도 일차함수와 비슷한 느낌의 선형 그래프가 만들어지지 않았을까 싶다.  다만 이렇게 변수를 정하면 변수가 독립, 종속으로 기존 코드와 다르게 2개가 되므로 코드가 조금 바뀔 것 같다.  성능 같은 경우에는 처음에 언급했다싶이 알고리즘 자체가 성능을 중시한 것이 아닌 것 같다는 느낌을 많이 받았다. 그만큼 느리다는 것. 다만 이 역시도 결과가 나오지 않아서 추측해 본 것에 불과하다. */

