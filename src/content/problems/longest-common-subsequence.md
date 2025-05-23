---
title: "Longest Common Subsequence"
tags:
  - leetcode
  - string
  - sequence
  - recursion
  - memoization
  - dp
  - python
date: 2022-10-13
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/OdrC-Zhw6sw?si=CTB_0USdLDXXaKjA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

LeetCode의 1143번째 문제인 [Longest Common Sequence](https://leetcode.com/problems/longest-common-subsequence/)를 함께 풀어보도록 하겠습니다.

## 문제

두 개의 문자열 `text1`과 `text2`가 주어졌을 때 두 문자열의 가장 긴 공통 부분 수열의 길이를 반환하시오.
공통 부분 수열이 없는 경우에는 `0`을 반환하시오.

문자열의 부분 수열은 원래 문자열에서 일부 글자들을 삭제하되 남은 문자들의 상대적인 순서를 바꾸지 않은 새로운 문자열입니다.

예를 들어, `ace`는 `abcde`의 부분 수열입니다.

두 문자열의 공통 부분 수열은 두 문자열 모두에 공통으로 있는 부분 수열입니다.

## 예제

```py
입력: text1 = "abcde", text2 = "ace"
출력: 3
```

```py
입력: text1 = "abc", text2 = "abc"
출력: 3
```

```py
입력: text1 = "abc", text2 = "def"
출력: 0
```

## 풀이 1

문제에서 주어진 첫 번째 예제를 기준으로 어떻게 가장 긴 공통 부분 수열의 길이를 구할 수 있는지 생각을 해보겠습니다.

우선 두 문자열의 첫 번째 글자를 비교해보면 `a`와 `a`로 일치하는데요.
이 글자 자체가 공통 부분 수열이 될 수 있으므로 가장 긴 공통 부분 수열의 길이는 최소 `1`이라는 것을 알 수 있습니다.

```
_____
abcde
___
ace
```

일치하는 `a`를 제외하고 남은 두 부분 문자열을 이동해보면, 이 번에는 첫 번째 글자가 `b`와 `c`로 일치하지 않는데요.
여기서 우리에게는 두 가지 선택 사항이 생깁니다.

```
  ____
a bcde
  __
a ce
```

첫 번째 옵션은 첫 번째 부분 문자열의 첫 번째 글자인 `b`를 제외하고 다시 비교해보는 것입니다.
이 경우, 두 부분 문자열의 첫 번째 글자가 `c`와 `c`로 일치하게 되어 가장 공통 부분 수열이 `ac`로 길어집니다.

```
   ___
ab cde
   __
a  ce
```

두 번째 옵션은 두 번째 부분 문자열의 첫 번째 글자인 `c`를 제외하고 다시 비교해보는 것입니다.
이 경우, 두 부분 문자열의 첫 번째 글자가 `b`와 `e`로 일치하지 않으므로, 공통 부분 수열의 길이에 영향을 주지 않으며,
우리에게는 다시 2가지 선택 사항이 생깁니다.

```
   ____
a  bcde
   _
ac e
```

첫 번째 경우, 부분 문자열의 첫 글자가 일치하지 않으므로, 여기서 추가적으로 2가지 선택 사항이 생기겠죠?
지금까지 거쳤던 동일한 과정을 반복해야할 것입니다.

```
   ____
ab cde
   _
ac e
```

두 번째 경우에는 두 번째 문자열이 끝에 다달았으므로 더 이상 비교할 글자가 없게 됩니다.
여기서 경로는 더 이상 들어갈 수가 없습니다.

```
   ____
a  bcde
   _
ace
```

지금까지 사고 과정을 살펴보면 이 문제는 재귀 알고리즘으로 해결할 수 있다는 것을 알 수 있는데요.
두 문자열의 첫 글자가 동일하다면 가장 긴 공통 부분의 수열의 길이를 `1` 증가 시키고, 두 문자열에서 첫 글자를 제외하고 남은 문자열을 상대로 비교를 계속해야합니다.
두 문자열의 첫 글자가 동일하지 않다면 두 문자열을 번갈아 가며 첫 글자를 제외하고 총 2번의 비교를 해야합니다.

그럼 이 알고리즘을 코드로 구현해보겠습니다.

```py
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        def dfs(i, j):
            if i == len(text1) or j == len(text2):
                return 0
            if text1[i] == text2[j]:
                return 1 + dfs(i + 1, j + 1)
            return max(dfs(i + 1, j), dfs(i, j + 1))

        return dfs(0, 0)
```

재귀 알고리즘의 이해를 돕기 위해서 전체 호출 스택을 시각화해보았습니다.

```py
"abcde", "ace" => 1 + 2 = 3
    "bcde", "ce" => MAX(2, 1) = 2
        "cde", "ce" => 1 + 1 = 2
            "de", "e" => MAX(1, 0) = 1
                "e", "e" => 1 + 0
                    "", "" => 0
                "de", "" => 0
        "bcde", "e" => MAX(1, 0) = 1
            "cde", "e" => MAX(1, 0) = 1
                "de", "e" => MAX(1, 0) = 1
                    "e", "e" => 1 + 0
                        "", "" => 0
                    "de", "" => 0
                "cde", "" => 0
            "bcde", "" => 0
```

첫 번째 문자열의 길이를 `m`, 두 번째 문자열의 길이를 `n`이라고 했을 때 이 풀이의 복잡도는 어떻게 될까요?
재귀 함수 내에서 재귀 호출이 최대 두 번 일어날 수 있고, 최악의 경우 두 문자열 간에 일치하는 글자가 하나도 없어서 호출 스택이 두 문자열의 길이의 곱만큼 깊어질 수 있습니다.
따라서 시간 복잡도는 `O(2^(m * n))`이고, 공간 복잡도는 `O(m * n)`이 되겠습니다.

## 풀이 2

위에서 시각화해드린 호출 트리를 살펴보시면 중복 연산이 관찰이 되는데요.

예를 들면, `cde`와 `ce` 간에 가장 긴 공통 부분 수열의 길이를 구하기 위해서 `de`와 `e` 간에 가장 긴 공통 부분 수열의 길이를 구하는데요.
`cde`와 `e`간에 가장 긴 공통 부분 수열의 길이를 구할 때도 동일하게 `de`와 `e` 간에 가장 긴 공통 부분 수열의 길이를 구하고 있습니다.

```py
"de", "e" => MAX(1, 0) = 1
    "e", "e" => 1 + 0
        "", "" => 0
    "de", "" => 0
```

[메모이제이션(Memoization)](/algorithms/memoization/) 기법을 활용하면 재귀 알고리즘에서 중복 연산을 효과적으로 제거할 수 있는데요.
이 경우, 재귀 함수의 인자로 `de`와 `e`가 넘어왔을 때의 결과 값을 저장해두면, 재귀 함수가 동일한 인자로 호출이 되었을 때 저장해둔 값을 사용할 수 있습니다.

그럼 [해시 테이블(Hash Table)](/data-structures/hash-table/)에 호출 결과를 저장해두고 활용하도록 코드를 수정해보겠습니다.

```py
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        memo = {}

        def dfs(i, j):
            if (i, j) in memo:
                return memo[(i, j)]
            if i == len(text1) or j == len(text2):
                memo[(i, j)] = 0
            elif text1[i] == text2[j]:
                memo[(i, j)] = 1 + dfs(i + 1, j + 1)
            else:
                memo[(i, j)] = max(dfs(i + 1, j), dfs(i, j + 1))
            return memo[(i, j)]

        return dfs(0, 0)
```

참고로 `@cache` 데코레이터를 사용하시면 좀 더 간편하게 메모이제이션 효과를 얻을 수 있습니다.

```py
from functools import cache

class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        @cache
        def dfs(i, j):
            if i == len(text1) or j == len(text2):
                return 0
            if text1[i] == text2[j]:
                return 1 + dfs(i + 1, j + 1)
            return max(dfs(i + 1, j), dfs(i, j + 1))

        return dfs(0, 0)
```

이 풀이의 시간 복잡도는 중복 연산이 모두 제거되어 `O(m * n)`로 대폭 향상됩니다.

## 풀이 3

여태까지는 어떤 인덱스에서 **시작하는** 부분 문자열 간에 가장 긴 공통 부분 수열의 길이를 구했었는데요.
즉, 더 작은 인덱스에 대한 결과를 구하기 위해서 더 큰 인덱스에 대한 결과가 필요했었고 그래서 재귀 알고리즘을 사용해야 했습니다.

이번에는 반대로 어떤 인덱스에서 **끝나는** 부분 문자열 간에 가장 긴 공통 부분 수열의 길이를 구해보면 어떨까요?
그러면 [동적 계획법(Dynamic Programming)](/algorithms/dp/)을 통해서 이차원 배열을 사용하여 반복 알고리즘으로 해결을 할 수 있을 것입니다.

첫 번째 문자열에서 인덱스 `i`에 있는 글자와 두 번째 문자열에서 인덱스 `j`에 있는 글자가 동일하다면,
첫 번째 문자열에서 인덱스 `i - 1`에서 끝나는 부분과 두 번째 문자열에서 인덱스 `j - 1`에서 끝나는 부분 간에 가장 긴 공통 부분 수열의 길이에 `1`을 더하면,
첫 번째 문자열에서 인덱스 `i`에서 끝나는 부분과 두 번째 문자열에서 인덱스 `j`에서 끝나는 부분 간에 가장 긴 공통 부분 수열의 길이가 됩니다.

```py
dp[i][j] = 1 + dp[i - 1][j - 1]
```

첫 번째 문자열에서 인덱스 `i`에 있는 글자와 두 번째 문자열에서 인덱스 `j`에 있는 글자가 동일하지 않다면,
첫 번째 문자열에서 인덱스 `i - 1`에서 끝나는 부분과 두 번째 문자열에서 인덱스 `j`에서 끝나는 부분 간에 가장 긴 공통 부분 수열의 길이와
첫 번째 문자열에서 인덱스 `i`에서 끝나는 부분과 두 번째 문자열에서 인덱스 `j - 1`에서 끝나는 부분 간에 가장 긴 공통 부분 수열의 길이 중에 더 큰 값이
첫 번째 문자열에서 인덱스 `i`에서 끝나는 부분과 두 번째 문자열에서 인덱스 `j`에서 끝나는 부분 간에 가장 긴 공통 부분 수열의 길이가 됩니다.

```py
dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
```

문제에서 주어진 첫 번째 예제를 생각을 해보면 다음과 같은 이차원 배열이 만들어질 것입니다.

|         | ""  | "a"           | "ac"          | "ace"         |
| ------- | --- | ------------- | ------------- | ------------- |
| ""      | 0   | 0             | 0             | 0             |
| "a"     | 0   | 0 + 1 = 1     | MAX(0, 1) = 1 | MAX(0, 1) = 1 |
| "ab"    | 0   | MAX(1, 0) = 1 | MAX(1, 1) = 1 | MAX(1, 1) = 1 |
| "abc"   | 0   | MAX(1, 0) = 1 | 1 + 1 = 2     | MAX(1, 2) = 2 |
| "abcd"  | 0   | MAX(1, 0) = 1 | MAX(2, 1) = 2 | MAX(2, 2) = 2 |
| "abcde" | 0   | MAX(1, 0) = 1 | MAX(2, 1) = 2 | 1 + 2 = 3     |

지금까지 설명드린 알고리즘을 코드로 구현해볼까요?

```py
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        dp = [[0] * (len(text2) + 1) for _ in range(len(text1) + 1)]
        for r in range(1, len(text1) + 1):
            for c in range(1, len(text2) + 1):
                if text1[r - 1] == text2[c - 1]:
                    dp[r][c] = 1 + dp[r - 1][c - 1]
                else:
                    dp[r][c] = max(dp[r - 1][c], dp[r][c - 1])
        return dp[-1][-1]
```

이 풀이는 메모이제이션을 적용한 재귀 알고리즘 풀이와 동일한 복잡도를 갖습니다.
이중 루프를 돌고, 이차원 배열을 사용하기 때문입니다.
