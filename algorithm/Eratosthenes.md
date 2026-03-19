# 소수판별 및 에라토스테네스의 체 활용
+ 특정 숫자의 소수 판별
+ n까지의 소수 판별
+ n까지의 약수 개수 구하기
#
<br>

## 1. 임의의 자연수 $N$의 소수 판별
특정 숫자 하나가 소수인지 확인할 때 사용합니다.

### 원리
- 소수는 1과 자기 자신만을 약수로 갖는 수입니다.
- $N$의 약수는 $\sqrt{N}$을 기준으로 대칭을 이루므로, **2부터 $\sqrt{N}$까지**만 나누어떨어지는지 확인하면 충분합니다.
- **시간 복잡도**: $O(\sqrt{N})$

```cpp
bool isPrime(int n) {
    if (n < 2) {
        return false;
    }
    
    // sqrt(n)까지 확인, sqrt함수 대신 i*i 사용
    for (int i = 2; i * i <= n; ++i) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

---

## 2. 에라토스테네스의 체 (1부터 $N$까지의 소수 판별)
범위 내의 모든 숫자에 대해 소수 여부를 미리 구해놓고 참조해야 할 때 사용합니다.

### 원리
1. 2부터 $N$까지 모든 수를 나열합니다.
2. 아직 지워지지 않은 수 중 가장 작은 수 $P$를 소수로 선택합니다.
3. $P$의 **배수들을 모두 지웁니다.** (단, $P$ 자체는 남깁니다.)
4. 위 과정을 반복하면 남은 수들이 모두 소수가 됩니다.
- **시간 복잡도**: $O(N \log (\log N))$

```cpp
#include <vector>

std::vector<bool> GetPrimeNumbers(int n) {
    std::vector<bool> numbers(n + 1, true);
    numbers[0] = numbers[1] = false;

    for (int i = 2; i * i <= n; ++i) {
        if (numbers[i]) {
            for (int j = i * i; j <= n; j += i) {
                numbers[j] = false;
            }
        }
    }
    return numbers;
}
```

---

## 3. 에라토스테네스의 체를 이용한 약수 개수 구하기
범위 내 모든 수($1 \sim N$)의 약수 개수를 한꺼번에 구해야 할 때 사용합니다.

### 원리
- 에라토스테네스의 체에서 배수를 지우는 대신, **배수 위치의 카운트를 1씩 증가**시킵니다.
- $i$가 1부터 $N$까지 돌 때, $i$의 배수들은 모두 $i$를 약수로 가지기 때문입니다.

### C++ 구현 코드
```cpp
#include <vector>

std::vector<int> CountAllDivisors(int n) {
    std::vector<int> count(n + 1, 0);

    for (int i = 1; i <= n; ++i) {
        for (int j = i; j <= n; j += i) {
            ++count[j];
        }
    }
    return count;
}
```
