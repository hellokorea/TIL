# Dynamic Programming
- 다이나믹 프로그래밍에 대해 설명을 기재한다.

<br>

## 정의
- 쉽게 말해 어떠한 문제를 해결하고자 할 때 큰 문제를 작게 나누어 해답을 찾는 알고리즘 해결 방법이다.
- 하위 문제를 해결 했을 경우 그 결과를 MemoryZation이란 곳에 저장하여 중복 문제를 해결 할 수 있어 연산 속도가 비약적으로 증가한다. 
  - MemoryZation -> Top - Down

<br>

## DP 알고리즘이 적용 가능한 문제
#### 최적 구조
- 동일한 작은 문제들을 반복적으로 해결이 가능할 경우

<br>

#### 중복 문제
- 최적 부분 문제를 해결을 하위 문제의 최적 해결 방법으로 사용이 가능 할 경우

<br>

## 피보나치 수열 
- DP 알고리즘을 적용하여 효율적으로 해결할 수 있는 대표적인 문제이다.
- 기본적으로 재귀 호출을 통해 해결이 가능하며 다음과 같이 값을 return 한다.

<br>

```Java
return p(n) = (n -1) + (n +2)
```

<br>

## Code ( Top - Down )
- 코드 예시를 통해 한 번 예를 들어보자. 

<br>

```Java
public class Main {
    static long[] memo;

    public static void main(String[] args) {
        int x = 5;

        memo = new long[x + 1];

        System.out.println(fibo(x));
    }

    static long fibo(int x) {

        if (x == 1 || x == 2) {
            return 1;
        }
        // 메모이제이션 : 이미 계산된 값이 있는 경우, 그 값을 반환
        if (memo[x] != 0) {
            return memo[x];
        }
        // 피보나치 수 계산 및 메모이제이션 배열에 저장
        memo[x] = fibo(x - 1) + fibo(x - 2);
        return memo[x];
    }
}    
```

<br>

### 실행 단계
- 먼저 memo라는 배열을 생성하여 재귀 함수 값이 호출 될 때 각 i에 저장할 값을 저장하기 위한 메모리제이션 배열을 만들어준다.

<br>

### Flow
- memo[x] 원소 값이 없으면 해당[x] 인덱스에 값을 넣어줄 재귀 함수를 호출한다.
- memo[1], memo[2]는 기저 조건이므로 = 1
- memo[3] = 1 + 1 => 2
- memo[4] = fibo(3), fibo(2) 호출 시 memo[3] = 2, memo[2] = 1 존재하므로 중복 호출을 방지.
- memo[4] = 2 + 1 => 3
- memo[5] = fibo(4), fibo(3) 호출 시 memo[4] = 3, memo[3] = 2 존재하므로 중복 방지.
- memo[5] = 3 + 2 => 5 가 최종적으로 return.

<br>

### 정리
- 즉, return 된 memo[x]의 값을 각각의 memo[x]에 계산 된 값을 저장하여 중복을 방지하여 비효율적인 호출을 예방한다.

<br>

## Code2 ( Top - Down )
- 이번엔 1로 만들기 라는 문제를 해결해보도록 하자.
- DP를 적용하여 해결할 수 있는 대표적인 알고리즘 문제이다.

<br>

### 설명
- 정수 X가 주어질 때 4가지 연산을 할 수 있다.
1. X가 5로 나누어 떨어지면, 5로 나눈다.
2. X가 3으로 나누어 떨어지면, 3으로 나눈다.
3. X가 2로 나누어 떨어지면, 2로 나눈다.
4. X에서 1을 뺀다
- 이 때 X값이 주어졌을 때 1을 만들기 위한 최소한의 연산 횟 수를 구해라.

<br>

```Java
public class Main {
    static intll memo = new int [30001];

    public static void main (String[] args) {
        Scanner sc = new Scanner (System. in);
        int x = sc.nextInt();

        for (int i = 2; i <= x; i++) {
            
            memo[i] = memo[i - 1] + 1;

             if (memo[i] % 2) {
                memo[i] = Math.min(memo[i], memo[i / 2] + 1);
            }    

            if (memo[i] % 3) {
                memo[i] = Math.min(memo[i], memo[i / 3] + 1);
            }    

             if (memo[i] % 5) {
                memo[i] = Math.min(memo[i], memo[i / 5] + 1);
            }        
        }

        System.out.println(memoi[x]);
    }
/*
26 <- x
3 <- memo[x]
*/
}
```

<br>

### 실행 단계
- 피보나치 수열을 해결했을 때 처럼 memo 배열을 생성 해준다.
  - 여기서 입력 조건은 1 <= x <= 30,000

<br>

### Flow
- i = 2 일 때 memo[2] = memo[1] + 1 => memo[2] = 1 
  - 즉, 만약 x 값이 2일경우 최소한의 연산은 1회이다.
- i = 3 이면 memo[3] = memo[2] + 1 => memo[3] = 2
  - 이 때 i가 3이므로, if 조건에 충족하여 추가 로직을 수행한다.
  - Math.min(memo[3], memo[3 / 3] + 1) => 2, 1을 비교하여 최소값을 도출하여 memo[i]에 할당한다.
  - 최종적으로 memo[3] = 1

<br>

- 26까지 반복 ..........

<br>

- i = 26 이면, memo[25] = 3, memoi[13] + 1 = 3 / memoi[26] = Math.min(3, 3) => 3
- 최종적으로 memo[26] = 3 이므로, x = 26 일 때 최소 연산 횟 수는 3이 도출 되는 것이다.

<br>

### 정리
- 이 문제도 피보나치 수열처럼 큰 문제를 작은 문제로 쪼개어 MemoryZation을 활용하여 이전에 계산했던 값을 바탕으로 처리하는 DP이다.

<br>

## DP Tip!
- DP로 푸는 문제는 먼저 이 문제의 작은 문제를 구조화하는 것부터 시작하는 것이 좋다.
- dp[0], dp[1], dp[2], dp[3] 이렇게 작은 문제를 점차 해결 하다 보면 규칙을 발견할 수 있을 것이고 dp[4]를 해결할 즈음에는 이전에 구해놓은 작은 문제들을 이용하여 점화식을 도출 하는 것
- 접근, 분리, 반복, 점화식, state 결정 등을 통해 문제를 보다 쉽게 해결할 수 있다.
- 추가적으로 Bottom - Up 방법도 있지만, 추후 기술 해보겠다.