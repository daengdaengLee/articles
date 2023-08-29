# 155. Min Stack

[문제 링크](https://leetcode.com/problems/min-stack/)

## 요구사항

- push, pop, top, min 기능을 제공하는 스택을 구현하라.
- min 기능은 상수 시간 안에 동작해야 한다.

MinStack class 구현:

- `MinStack()` 은 스택 객체를 초기화한다.
- `void push(int val)` 은 원소 `val` 을 스택에 추가한다.
- `void pop()` 은 스택에서 제일 위에 있는 원소를 제거한다.
- `int top()` 은 스택에서 제일 위에 있는 원소를 반환한다.
- `int getMin()` 은 스택의 원소들 중 최솟값을 반환한다.

각 기능은 모두 `O(1)` 시간 복잡도를 가지도록 구현해야 한다.

제약:

- `-(2 ^ 31) <= val <= (2 ^ 31) - 1`
- `pop`, `top`, `getMin` 메소드는 항상 원소가 있을 때만 호출된다.
- `push`, `pop`, `top`, `getMin` 은 최대 `3 * (10 ^ 4)` 번 호출된다.

## 예시

입력:

- 메소드 호출: `["MinStack","push","push","push","getMin","pop","top","getMin"]`
- 메소드 인자: `[[],[-2],[0],[-3],[],[],[],[]]`

출력:

- `[null,null,null,null,-3,null,0,-2]`

설명:

```text
// 실행 예시

MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top(); // return 0
minStack.getMin(); // return -2
```

## 아이디어

- 어떤 원소를 추가했을 때 최솟값은 해당 원소가 다시 top 위치에 왔을 때 항상 동일하다.
- 스택은 원소의 순서를 유지하고 LIFO 형식으로 동작하기 때문에 중간 원소를 제거할 수 없기 때문이다.
- 원소를 추가할 때마다 이전 최솟값과 추가한 원소를 비교하여 새 최솟값을 구하고 해당 원소와 쌍을 이루어 저장한다.

```text
class MinStack {
    원소_탑
    최솟값_탑

    MinStack() {
      원소_탑 = null
      최솟값_탑 = null
    }
    
    push(val) {
      원소_탑 = 노드(val, 원소_탑)
      최솟값 = 최솟값_탑 == null 이면 val 아니면 min(val, 최솟값_탑.value)
      최솟값_탑 = 노드(min, 최솟값_탑)
    }
    
    pop() {
      기존_원소_탑 = 원소_탑
      원소_탑 = 기존_원소_탑.next
      기존_원소_탑.next = null
      
      기존_최솟값_탑 = 최솟값_탑
      최솟값_탑 = 기존_최솟값_탑.next
      기존_최솟값_탑.next = null
    }
    
    top() {
      return 원소_탑.value 
    }
    
    getMin() {
      return 최솟값_탑.value 
    }
}

class Node {
  value
  next
}
```

시간 복잡도:

- 모든 기능은 고정된 숫자의 연산만 사용하고 있다.
- 따라서 시간 복잡도는 `O(1)` 이다.

## 구현

```java
class MinStack {
    private Node topNode;
    private Node topMinNode;

    public MinStack() {
        this.topNode = null;
        this.topMinNode = null;
    }

    public void push(int val) {
        this.topNode = new Node(val, this.topNode);
        this.topMinNode = new Node(
                this.topMinNode == null ? val : Math.min(val, this.topMinNode.getValue()),
                this.topMinNode);
    }

    public void pop() {
        var topNode = this.topNode;
        this.topNode = topNode.getNext();
        topNode.setNext(null);

        var topMinNode = this.topMinNode;
        this.topMinNode = topMinNode.getNext();
        topMinNode.setNext(null);
    }

    public int top() {
        return this.topNode.getValue();
    }

    public int getMin() {
        return this.topMinNode.getValue();
    }

    private static class Node {
        private final int value;
        private Node next;

        public Node(int value, Node next) {
            this.value = value;
            this.next = next;
        }

        public int getValue() {
            return this.value;
        }

        public Node getNext() {
            return this.next;
        }

        public void setNext(Node next) {
            this.next = next;
        }
    }
}
```

실행 결과: Accepted

- Runtime 4 ms, Beats 99.19%
- Memory 46.8 MB, Beats 93.96%

## 다른 방법

- 실제 원소를 보관하는 스택과 최솟값을 보관하는 스택을 따로 관리하지 않고 한 노드에서 실제 원소와 최솟값을 모두 보관하도록 구현할 수 있다.
- 동작 원리와 시간 복잡도는 기존과 동일하다.

```java
class MinStack {
    private Node topNode;

    public MinStack() {
        this.topNode = null;
    }

    public void push(int val) {
        this.topNode = new Node(
                val,
                this.topNode == null ? val : Math.min(val, this.topNode.getMin()),
                this.topNode);
    }

    public void pop() {
        var topNode = this.topNode;
        this.topNode = topNode.getNext();
        topNode.setNext(null);
    }

    public int top() {
        return this.topNode.getValue();
    }

    public int getMin() {
        return this.topNode.getMin();
    }

    private static class Node {
        private final int value;
        private final int min;
        private Node next;

        public Node(int value, int min, Node next) {
            this.value = value;
            this.min = min;
            this.next = next;
        }

        public int getValue() {
            return this.value;
        }

        public int getMin() {
            return this.min;
        }

        public Node getNext() {
            return this.next;
        }

        public void setNext(Node next) {
            this.next = next;
        }
    }
}
```

실행 결과: Accepted

- Runtime 4 ms, Beats 99.19%
- Memory 47.1 MB, Beats 51.3%
