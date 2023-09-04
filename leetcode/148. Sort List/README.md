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

## 아이디어

- 시간 복잡도 `O(n * log n)` 을 갖는 병합 정렬 (Merge Sort) 을 사용한다.
- 노드의 순서를 추가 메모리 공간 없이 바꿀 수 있는 연결 리스트의 특징을 이용한다.

```text
fn 해법(머리_노드):
  전체_길이 = 전체_길이_구하기(머리_노드)
  if 전체_길이 <= 1:
    return 머리_노드

  부분_길이 = 1
  while 부분_길이 < 전체_길이:
    부분_머리_꼬리 = 부분_정렬하기(머리_노드, 부분_길이)
    머리_노드 = 부분_머리_꼬리.머리_노드
    부분_꼬리_노드 = 부분_머리_꼬리.꼬리_노드
    
    while 부분_꼬리_노드.next != null: 
      부분_머리_꼬리 = 부분_정렬하기(부분_꼬리_노드.next, 부분_길이)
      부분_꼬리_노드.next = 부분_머리_꼬리.머리_노드
      부분_꼬리_노드 = 부분_머리_꼬리.꼬리_노드
      
    부분_길이 <- 2배
  
  return 머리_노드
  
fn 전체_길이_구하기(머리_노드):
  전체_길이 = 0
  while 머리_노드 != null:
    전체_길이 <- 1 증가
    머리_노드 = 머리_노드.next
  return 전체_길이
  
record 부분_머리_꼬리(머리_노드, 꼬리_노드)

fn 부분_정렬하기(머리_노드, 부분_길이):
  머리_노드 = null
  현재_노드 = null
  
  앞쪽_카운트 = 부분_길이
  뒤쪽_카운트 = 부분_길이
  
  중간_노드 = 최대N개_이후_노드_구하기(노드, 부분_길이 - 1)
  if 중간_노드.next == null:
    return 부분_머리_꼬리(노드, 중간_노드)
    
  앞쪽_노드 = 노드
  뒤쪽_노드 = 중간_노드.next
  
  while 앞쪽_카운트 > 0 이거나 뒤쪽_카운트 > 0:
    if 앞쪽_카운트 == 0:
      for 0 이상, 뒤쪽_카운트 미만:
        if 뒤쪽_노드 == null:
          break
        현재_노드.next = 뒤쪽_노드
        현재_노드 = 뒤쪽_노드
        뒤쪽_노드 = 뒤쪽_노드.next
      break
    
    if 뒤쪽_카운트 == 0 이거나 뒤쪽_노드 == null:
      for 0 이상, 앞쪽_카운트 미만:
        현재_노드.next = 앞쪽_노드
        현재_노드 = 앞쪽_노드
        앞쪽_노드 = 앞쪽_노드.next
      break
    
    if 앞쪽_노드.val <= 뒤쪽_노드.val:
      if 현재_노드 == null:
        머리_노드 = 앞쪽_노드
        현재_노드 = 앞쪽_노드 
      else:
        현재_노드.next = 앞쪽_노드
        현재_노드 = 앞쪽_노드
      앞쪽_노드 = 앞쪽_노드.next
      앞쪽_카운트 <- 1 감소
    else:
      if 현재_노드 == null:
        머리_노드 = 뒤쪽_노드
        현재_노드 = 뒤쪽_노드 
      else:
        현재_노드.next = 뒤쪽_노드
        현재_노드 = 뒤쪽_노드
      뒤쪽_노드 = 뒤쪽_노드.next
      뒤쪽_카운트 <- 1 감소

  현재_노드.next = 뒤쪽_노드
  return 부분_머리_꼬리(머리_노드, 현재_노드)

fn 최대N개_이후_노드_구하기(노드, n):
  마지막_노드 = 노드
  for 0 이상, n 미만:
    if 노드 == null:
      return 마지막_노드
    마지막_노드 = 노드
    노드 = 노드.next
  return 노드 == null 이면 마지막_노드 아니면 노드 
```

시간 복잡도:

- 바깥 루프에서 부분 길이를 2배씩 늘려가며 전체 길이에 도달할 때까지 반복하므로 `O(log n)` 만큼 시간 복잡도를 가진다.
  일반적인 병합 정렬의 분할 과정을 응용하여 사용했다.
  임의 인덱스 접근이 어렵기 때문에 전체를 나눠가는 방법 대신, 부분을 묶어가면서 전체에 도달하는 방법을 사용했다.
- 안쪽 루프에서 링크드 리스트의 맨 앞 노드에서 맨 뒤 노드까지 순회하므로 `O(n)` 만큼 시간 복잡도를 가진다.
- 전체 시간 복잡도는 `O(n * log n)` 이다. (병합 정렬과 동일한 원리이다.)

공간 복잡도:

- 링크드 리스트의 순서 변경은 next 참조 변경으로 가능하다.
- 정렬된 리스트를 담을 별도의 컬렉션 메모리 공간이 필요 없다.
- 공간 복잡도는 `O(1)` 이다.

## 구현

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        var len = this.getLength(head);
        if (len <= 1) {
            return head;
        }

        var localLen = 1;
        while (localLen < len) {
            var headTail = this.sortLocal(head, localLen);
            head = headTail.head;
            var tail = headTail.tail;
            while (tail.next != null) {
                headTail = this.sortLocal(tail.next, localLen);
                tail.next = headTail.head;
                tail = headTail.tail;
            }
            localLen *= 2;
        }

        return head;
    }

    private int getLength(ListNode head) {
        var len = 0;
        while (head != null) {
            len += 1;
            head = head.next;
        }
        return len;
    }

    private ListNode offsetAtMostN(ListNode node, int offset) {
        var lastNode = node;
        for (var i = 0; i < offset; i += 1) {
            if (node == null) {
                return lastNode;
            }
            lastNode = node;
            node = node.next;
        }
        return node == null ? lastNode : node;
    }

    private HeadTail sortLocal(ListNode node, int localLen) {
        ListNode head = null;
        ListNode current = null;

        int aCount = localLen;
        int bCount = localLen;

        var midNode = this.offsetAtMostN(node, localLen - 1);
        if (midNode.next == null) {
            return new HeadTail(node, midNode);
        }

        var aNode = node;
        var bNode = midNode.next;

        while (aCount > 0 || bCount > 0) {
            if (aCount == 0) {
                for (var i = 0; i < bCount; i += 1) {
                    if (bNode == null) {
                        break;
                    }
                    current.next = bNode;
                    current = bNode;
                    bNode = bNode.next;
                }
                break;
            }

            if (bCount == 0 || bNode == null) {
                for (var i = 0; i < aCount; i += 1) {
                    current.next = aNode;
                    current = aNode;
                    aNode = aNode.next;
                }
                break;
            }

            if (aNode.val <= bNode.val) {
                if (current == null) {
                    head = aNode;
                    current = aNode;
                } else {
                    current.next = aNode;
                    current = aNode;
                }
                aNode = aNode.next;
                aCount -= 1;
            } else {
                if (current == null) {
                    head = bNode;
                    current = bNode;
                } else {
                    current.next = bNode;
                    current = bNode;
                }
                bNode = bNode.next;
                bCount -= 1;
            }
        }

        current.next = bNode;

        return new HeadTail(head, current);
    }

    private record HeadTail(ListNode head, ListNode tail) {
    }
}
```

실행 결과: Accepted

- Runtime 14 ms, Beats 27.80%
- Memory 55.8 MB, Beats 51.89%
