# 530. Minimum Absolute Difference in BST

[문제 링크](https://leetcode.com/problems/minimum-absolute-difference-in-bst/)

## 요구사항

Binary Search Tree (BST) 의 루트가 주어질 때, 트리에 존재하는 임의의 두 노드의 차이의 절댓값중 최솟값을 구해라.

제약:

- 트리의 존재하는 노드 개수는 `2` 이상 `(10 ^ 4)` 이하이다..
- `0 <= Node.val <= (10 ^ 5)`

## 예시

Example 1:

- 입력:
  ```text
      4 (root)
     / \ 
    2   6
   / \
  1   3
  ```
- 출력: `1`

Example 2:

- 입력:
  ```text
      1 (root)
     / \ 
    0   48
       /  \
      12  49
  ```
- 출력: `1`

## 아이디어

- BST 는 왼쪽 -> 가운데 -> 오른쪽 순서대로 오름차순으로 정렬된 상태이다.
- 가장 왼쪽부터 재귀적으로 깊이 우선 탐색하여 오름차순으로 정렬된 배열을 만든다.
- 인접한 값 사이의 차이 중 가장 작은 값 탐색한다.

```text
fn 해법(머리_노드);
  정렬된_배열 = 평탄화(머리_노드)
  최소_차이 = 정수_최댓값
  for 1 <= i < 길이(정렬된_배열):
    왼쪽_값 = 정렬된_배열[i - 1]
    오른쪽_값 = 정렬된_배열[i]
    현재_차이 = 오른쪽_값 - 왼쪽_값
    최소_차이 = 최솟값(최소_차이, 현재_차이);
  return 최소_차이
  
fn 평탄화(머리_노드):
  if 머리_노드 == null:
    return []
  return [...평탄화(머리_노드.왼쪽_노드), 머리_노드, ...평탄화(머리_노드.오른쪽_노드)]
```

시간 복잡도:

- 평탄화하면서 전체 노드를 한 번 순회한다.
- 이후 최소 차이를 구하기 위해 전체 노드를 다시 한 번 순회한다.
- 시간 복잡도는 `O(n + n) = O(n)` 이다.

공간 복잡도:

- 평탄화하면서 재귀 함수를 호출한다.
- 이 때 재귀의 최대 깊이는 이진 트리의 높이와 같으므로 공간 복잡도는 `O(log n)` 이다.
- 평탄화한 배열의 공간 복잡도는 `O(n)` 이다.
- 따라서 공간 복잡도는 `O(log n + n) = O(n)` 이다.

## 최적화

- 인접한 노드 사이의 차이만 계산하기 때문에 전체 노드를 배열에 저장할 필요가 없다.
- 탐색 과정에서 직전에 탐색한 노드를 추적하여 바로 차이를 계산해 최솟값을 업데이트한다.

```text
직전_탐색_값 = null
최소_차이 = 정수_최댓값

fn 해법(머리_노드);
  if 머리_노드 == null:
    return 최소_차이
    
  해법(머리_노드.왼쪽_노드)
  
  if 직전_탐색_값 != null:
    최소_차이 = 최솟값(최소_차이, 머리_노드.값 - 직전_탐색_값)
  직전_탐색_값 = 머리_노드.값
  
  해법(머리_노드.오른쪽_노드)
  
  return 최소_차이
```

시간 복잡도:

- 전체 노드를 한 번 순회하기 때문에 `O(n)` 이다.

공간 복잡도:

- 재귀 함수 호출은 그대로 트리의 높이만큼 최대한 깊어지기 때문에 공간 복잡도는 `O(log n)` 이다.
- 추가적인 배열을 생성하지 않기 때문에 전체 공간 복잡도도  `O(log n)` 이다.

## 구현

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private int minDiff = Integer.MAX_VALUE;
    private Integer prev = null;

    public int getMinimumDifference(TreeNode root) {
        if (root == null) {
            return this.minDiff;
        }

        this.getMinimumDifference(root.left);
        if (this.prev != null) {
            this.minDiff = Math.min(this.minDiff, root.val - this.prev);
        }
        this.prev = root.val;
        this.getMinimumDifference(root.right);

        return this.minDiff;

    }
}
```

실행 결과: Accepted

- Runtime 0 ms, Beats 100%
- Memory 43.5 MB, Beats 25.21%

## 다른 방법

- 기존 방법과 알고리즘은 동일하지만 재귀 함수를 사용하지 않고 스택을 이용하여 구현할 수 있다.
- 이 경우 시간 복잡도, 공간 복잡도는 동일하지만 스택 오버플로우 위험을 없앨 수 있다.
- 각 서브 트리에서 왼쪽 -> 가운데 -> 오른쪽 순서로 탐색해야 하기 때문에 스택에는 오른쪽 -> 가운데 -> 왼쪽 순서로 추가한다. (꺼낼 때 반대로 꺼내지기 때문)
- 각 노드는 자식 노드를 포함하여 순서대로 스택에 넣기 위해 한 번, 최소 차이를 계산하기 위해 한 번 스택에 추가된다.

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int getMinimumDifference(TreeNode root) {
        Integer prev = null;
        int minDiff = Integer.MAX_VALUE;
        var stack = new Stack<StackNode>();
        stack.push(new StackNode(root, false));
        while (!stack.isEmpty()) {
            var stackNode = stack.pop();
            var node = stackNode.node();
            if (!stackNode.isChildrenPushed()) {
                if (node.right != null) {
                    stack.push(new StackNode(node.right, false));
                }
                stack.push(new StackNode(node, true));
                if (node.left != null) {
                    stack.push(new StackNode(node.left, false));
                }
            } else {
                if (prev == null) {
                    prev = node.val;
                } else {
                    minDiff = Math.min(minDiff, node.val - prev);
                    prev = node.val;
                }
            }
        }
        return minDiff;
    }

    public record StackNode(TreeNode node, boolean isChildrenPushed) {
    }
}
```

실행 결과: Accepted

- Runtime 36 ms, Beats 5.11%
- Memory 44.2 MB, Beats 7.24%
- 성능이 전체적으로 나빠졌다.
- 힙 메모리를 사용하는 스택이 재귀 호출에서 사용하는 콜 스택보다 느리다.
- 자식 노드의 탐색 여부를 추적하기 위해 사용한 커스텀 레코드를 계속 생성하기 때문에 메모리도 더 많이 쓴다.
- 문제의 제약 사항을 고려하면 재귀 호출 방식을 사용해도 충분하다.
