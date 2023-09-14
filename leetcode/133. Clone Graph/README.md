# 133. Clone Graph

[문제 링크](https://leetcode.com/problems/clone-graph/)

## 요구 사항

- 무방향 연결 그래프(connected undirected graph)의 노드의 래퍼런스가 주어진다.
- 해당 그래프의 깊은 복사(deep copy, clone)를 반환하라.
- 노드는 정수 값과 이웃 노드의 목록을 가진다.

노드 형태:

```java
class Node {
    public int val;
    public List<Node> neighbors;
}
```

무방향 그래프?:

- 노드를 연결하는 간선에 방향이 없는 그래프이다.
- A - B 가 연결되어 있을 때 A 에서 B 로 갈 수 있고, B 에서 A 로도 갈 수 있다.

연결 그래프?:

- 그래프의 모든 노드가 서로 연결되어 있는 그래프이다.
- 그래프에서 임의로 두 개의 노드를 뽑았을 때, 항상 한쪽 노드에서 다른 노드로 갈 수 있다.

제약 사항:

- 노드는 0개 이상 100개 이하이다.
- `1 <= Node.val <= 100`
- `Node.val` 은 겹치지 않는다.
- 노드의 이웃 노드 목록에 같은 노드가 중복해서 들어가지 않는다.
- 노드의 이웃 노드 목록에 자기 자신은 없다.
- 그래프의 임의의 두 노드는 서로 연결되어 있다. (연결 그래프)

## 예시

Example 1:

- 입력:
  ```text
  A ----- B
  |       |
  |       |
  C ----- D
  ```
- 출력:
  ```text
  A' ----- B'
  |        |
  |        |
  C' ----- D'
  
  A.val == A'.val 이지만 A != A'
  B.val == B'.val 이지만 B != B'
  C.val == C'.val 이지만 C != C'
  D.val == D'.val 이지만 D != D'
  ```

## 아이디어

- 노드를 복사하는 함수에서 해당 노드를 복사하고 재귀적으로 이웃 노드를 복사한다.
- 연결 그래프이기 때문에 이웃 노드를 탐색하다 보면 다시 자신으로 돌아올 수 있다.
- 이미 처리한 노드 목록을 기록하여 이미 처리한 노드는 건너뛴다.

```text
class 노드:
  값
  이웃_노드_목록

def 그래프_복사(노드):
  복사한_노드_by_값 <- 맵[정수->노드] 생성
  return 노드_복사(노드, 복사한_노드_by_값)

def 노드_복사(노드, 복사한_노드_by_값):
  if 노드가 null 이면:
    return 노드
  
  복사한_노드 = 복사한_노드_by_값.get(노드.값)
  if 복사한_노드가 null 이 아니면:
    return 복사한_노드
    
  복사한_노드 <- 새 노드 생성
  복사한_노드.값 = 노드.값
  복사한_노드_by_값[복사한_노드.값] = 복사한_노드
  복사한_노드.이웃_노드_목록 = [노드_복사(이웃_노드, 복사한_노드_by_값) for 이웃_노드 in 노드.이웃_노드_목록]
  return 복사한_노드
```

시간 복잡도:

- 간선의 수에 비례하여 반복 횟수가 늘어난다.

공간 복잡도:

- 한 노드에서 다른 노드까지의 최대 거리만큼 재귀가 깊어진다.
- 재귀가 깊어지는 만큼 메모리를 사용한다.

## 구현

```java
package graph;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {
    public Node cloneGraph(Node node) {
        return this.cloneNode(node, new HashMap<>());
    }

    public Node cloneNode(Node node, Map<Integer, Node> clonedNodeMap) {
        if (node == null) {
            return null;
        }

        var clonedNode = clonedNodeMap.get(node.val);
        if (clonedNode != null) {
            return clonedNode;
        }

        clonedNode = new Node(node.val);
        clonedNodeMap.put(clonedNode.val, clonedNode);
        for (var neighbor : node.neighbors) {
            var clonedNeighbor = this.cloneNode(neighbor, clonedNodeMap);
            clonedNode.neighbors.add(clonedNeighbor);
        }
        return clonedNode;
    }

    static class Node {
        public int val;
        public List<Node> neighbors;

        public Node() {
            val = 0;
            neighbors = new ArrayList<Node>();
        }

        public Node(int _val) {
            val = _val;
            neighbors = new ArrayList<Node>();
        }

        public Node(int _val, ArrayList<Node> _neighbors) {
            val = _val;
            neighbors = _neighbors;
        }
    }
}
```

실행 결과: Accepted

- Runtime 26 ms, Beats 64.46%
- Memory 41.4 MB, Beats 94.68%

## 다른 방법

- 재귀 호출 대신 큐와 반복문을 사용할 수 있다.
- 트리 자료구조에 비유하면 재귀 호출 방법은 깊이 우선 탐색, 큐와 반복문을 이용한 방법은 너비 우선 탐색이라고 볼 수 있다.

```java
package graph;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;

class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }

        var map = new HashMap<Node, Node>();
        var queue = new LinkedList<Node>();
        queue.add(node);
        while (!queue.isEmpty()) {
            var originalNode = queue.poll();
            var clonedNode = map.get(originalNode);
            if (clonedNode == null) {
                clonedNode = new Node(node.val, new ArrayList<>());
                map.put(originalNode, clonedNode);
            }
            for (var originalNeighborNode : originalNode.neighbors) {
                var clonedNeighborNode = map.get(originalNeighborNode);
                if (clonedNeighborNode == null) {
                    clonedNeighborNode = new Node(originalNeighborNode.val, new ArrayList<>());
                    map.put(originalNeighborNode, clonedNeighborNode);
                    queue.add(originalNeighborNode);
                }
                clonedNode.neighbors.add(map.get(originalNeighborNode));
            }
        }
        return map.get(node);
    }

    static class Node {
        public int val;
        public List<Node> neighbors;

        public Node() {
            val = 0;
            neighbors = new ArrayList<Node>();
        }

        public Node(int _val) {
            val = _val;
            neighbors = new ArrayList<Node>();
        }

        public Node(int _val, ArrayList<Node> _neighbors) {
            val = _val;
            neighbors = _neighbors;
        }
    }
}
```

실행 결과: Accepted

- Runtime 25 ms, Beats 95.70%
- Memory 42 MB, Beats 34.60%
