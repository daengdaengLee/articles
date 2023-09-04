# 148. Sort List

[문제 링크](https://leetcode.com/problems/sort-list/)

## 요구사항

링크드 리스트의 머리 노드가 주어질 때, 오름차순으로 정렬한 링크드 리스트의 머리 노드를 반환해라.

단방향 링크드 리스트 정의:

```java
public class ListNode {
    int val;
    ListNode next;

    ListNode() {
    }

    ListNode(int val) {
        this.val = val;
    }

    ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
```

제약:

- 링크드 리스트의 노드 개수는 `0` 이상 `5 * (10 ^ 4)` 이하이다.
- `-(10 ^ 5) <= Node.val <= (10 ^ 5)`

추가 요구사항:

- `O(n * log n)` 시간 복잡도와 `O(1)` 공간 복잡도를 가지도록 구현하라.

## 예시

Example 1:

- 입력: `head = 4 -> 2 -> 1 -> 3`
- 출력: `head = 1 -> 2 -> 3 -> 4`

Example 2:

- 입력: `head = -1 -> 5 -> 3 -> 4 -> 0`
- 출력: `head = -1 -> 0 -> 3 -> 4 -> 5`

Example 3:

- 입력: `head = null`
- 출력: `head = null`
