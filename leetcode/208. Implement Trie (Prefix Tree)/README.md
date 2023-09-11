# 208. Implement Trie (Prefix Tree)

[문제 링크](https://leetcode.com/problems/implement-trie-prefix-tree/)

## 요구사항

Trie 클래스를 구현하라. :

- `Trie()`:
  trie 객체를 초기화한다.
- `void insert(String word)`:
  문자열 `word` 를 trie 객체에 추가한다.
- `boolean search(String word)`:
  문자열 `word` 가 trie 객체에 존재하면 `true` 를 아니면 `false` 를 리턴한다.
  정확하게 일치하는 문자열이 있어야 한다.
- `boolean startsWith(String prefix)`:
  문자열 `prefix` 를 접두사로 가지는 문자열이 trie 객체에 존재하면 `true` 를 아니면 `false` 를 리턴한다.
  정확하게 일치하는 경우도 포함한다.

Trie 란?:

- trie 혹은 prefix tree 는 효과적으로 문자열을 저장하고 조회할 수 있는 트리 자료구조이다.

제약 사항:

- `1 <= word.length, prefix.length <= 2000`
- `word` 와 `prefix` 는 알파벳 소문자로만 이루어져 있다.
- `insert`, `search`, `startsWith` 연산은 전체 합쳐서 최대 `3 * (10 ^ 4)` 번 호출된다.

## 예시

Example 1:

- 입력:
  ```text
  ["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
  [[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]] 
  ```
- 출력:
  ```text
  [null, null, true, false, true, null, true]
  ```
- 설명:
  ```text
  Trie trie = new Trie();
  trie.insert("apple");
  trie.search("apple");   // return True
  trie.search("app");     // return False
  trie.startsWith("app"); // return True
  trie.insert("app");
  trie.search("app");     // return True
  ```

## 아이디어

- 문자열을 문자 배열로 생각한다.
- 알파벳 소문자만 입력되는 제약사항이 있으므로 `char` 형으로 변환할 수 없는 문자는 고려하지 않는다.
- 각 문자를 트리의 노드에 대응시켜 한 층에 하나씩 저장한다.
- 중복되는 문자는 같은 노드를 재활용하고 다른 문자가 나올 때 새 노드를 트리에 추가한다.

```text
class 트라이:
  루트_노드
  
  fn 초기화():
    루트_노드 = new 노드(값 = ' ', 끝? = false, 자식_노드 = new 맵())
    
  fn 추가(단어):
    문자_배열 = (단어 -> 문자 배열로 변환)
    노드 = 루트_노드
    for 문자 in 문자_배열:
      자식_노드 = 노드.자식_찾기(문자)
      if 자식_노드 없으면:
        새_자식_노드 = new 노드(값 = 문자, 끝? = false, 자식_노드 = new 맵())
        노드.자식_추가(새_자식_노드)
        노드 = 새_자식_노드
      else:
        노드 = 자식_노드
    노드.끝? = true
  
  fn 탐색(단어):
    노드 = 노드_찾기(단어)
    if 노드 없으면:
      return false
    return 노드.끝?
  
  fn 접두사_탐색(접두사):
    노드 = 노드_찾기(단어)
    return 노드 있으면 ? true : false
  
  fn 노드_찾기(문자열):
    문자_배열 = (문자열 -> 문자 배열로 변환)
    노드 = 루트_노드
    for 문자 in 문자_배열:
      자식_노드 = 노드.자식_찾기(문자)
      if 자식_노드 없으면:
        return null
      노드 = 자식_노드
    return 노드
  
class 노드:
  값 // 해당 노드가 표현하는 문자
  끝? // 해당 노드가 문자열의 마지막인지 여부
  자식_노드 // [값->노드] 형태의 맵
  
  fn 자식_찾기(문자):
    if 자식_노드 에 문자 키가 존재하면:
      return 자식_노드[문자]
    return null
  
  fn 자식_추가(노드):
    자식_노드[노드.값] = 노드
```

시간 복잡도:

- 삽입, 조회 모두 입력한 문자열 길이만큼 순회한다.
- 그 이외의 연산은 모두 `O(1)` 의 시간 복잡도를 가진다.
- 따라서 입력 문자열 길이에 비례하는 `O(n)` 시간 복잡도를 가진다.

공간 복잡도:

- 모든 구성 문자가 새로 추가되는 경우 trie 자료구조는 문자열 길이만큼의 새 공간이 필요하다. 이 경우 공간 복잡도는 `O(n)` 이다.
- 하지만 더 많은 문자열을 추가할수록 중복되는 문자는 많아지고 추가 공간은 더 적게 필요해진다.
- 조회 과정에서는 고정된 크기의 메모리만 사용하므로 공간 복잡도는 `O(1)` 이다.

## 구현

```java
package trie;

import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

class Trie {
    private final Node root;

    public Trie() {
        this.root = new Node();
    }

    public void insert(String word) {
        var chars = word.toCharArray();
        var node = this.root;
        for (var c : chars) {
            var matched = node.getChild(c);
            if (matched.isEmpty()) {
                var newNode = new Node(c);
                node.addChild(newNode);
                node = newNode;
            } else {
                node = matched.get();
            }
        }
        node.isEnd = true;
    }

    public boolean search(String word) {
        var matched = this.findNode(word);
        return matched.map(n -> n.isEnd).orElse(false);
    }

    public boolean startsWith(String prefix) {
        var matched = this.findNode(prefix);
        return matched.isPresent();
    }

    private Optional<Node> findNode(String value) {
        var chars = value.toCharArray();
        var node = this.root;
        for (var c : chars) {
            var matched = node.getChild(c);
            if (matched.isEmpty()) {
                return matched;
            }
            node = matched.get();
        }
        return Optional.of(node);
    }

    private static final class Node {
        public final char value;
        public boolean isEnd;
        public final Map<Character, Node> children = new HashMap<>();

        public Node(char value) {
            this.value = value;
        }

        public Node() {
            this(' ');
        }

        public void addChild(Node child) {
            if (this.children.containsKey(child.value)) {
                throw new IllegalArgumentException("the character is already registerd.");
            }
            this.children.put(child.value, child);
        }

        public Optional<Node> getChild(char value) {
            return Optional.ofNullable(this.children.get(value));
        }
    }
}
```

실행 결과: Accepted

- Runtime 44 ms, Beats 27.70%
- Memory 54.7 MB, Beats 55.39%

## 다른 방법

- 반복문 대신 재귀를 사용하여 구현할 수 있다.
- 반복 횟수는 동일하므로 시간 복잡도는 동일하다.
- 조회의 경우 기존에는 `O(1)` 의 공간 복잡도를 가지지만, 재귀는 호출 스택을 문자열 길이만큼 쌓기 때문에 `O(n)` 의 공간 복잡도를 가진다.
- 삽입의 경우 공간 복잡도의 Big-O 노테이션은 동일하지만,
  재귀 호출 스택에 사용되는 공간이 문자열 길이만큼 필요하기 때문에 실제로는 메모리 공간을 2배 더 사용한다.

```java
package trie;

import java.util.HashMap;
import java.util.Map;

class Trie {
    private final Node root;

    public Trie() {
        this.root = new Node();
    }

    public void insert(String word) {
        this.root.insert(word, 0);
    }

    public boolean search(String word) {
        return this.root.search(word, 0);
    }

    public boolean startsWith(String prefix) {
        return this.root.startsWith(prefix, 0);
    }

    private static final class Node {
        public final char value;
        public boolean isEnd;
        public final Map<Character, Node> children = new HashMap<>();

        public Node(char value) {
            this.value = value;
        }

        public Node() {
            this(' ');
        }

        public void insert(String word, int idx) {
            if (idx >= word.length()) {
                return;
            }

            var c = word.charAt(idx);
            var node = this.children.get(c);
            if (node == null) {
                var newNode = new Node(c);
                this.children.put(newNode.value, newNode);
                node = newNode;
            }
            if (word.length() == idx + 1) {
                node.isEnd = true;
            }
            node.insert(word, idx + 1);
        }

        public boolean search(String word, int idx) {
            if (idx >= word.length()) {
                return false;
            }

            var c = word.charAt(idx);
            var node = this.children.get(c);
            if (node == null) {
                return false;
            }
            if (word.length() == idx + 1 && node.isEnd) {
                return true;
            }
            return node.search(word, idx + 1);
        }

        public boolean startsWith(String prefix, int idx) {
            if (idx >= prefix.length()) {
                return false;
            }

            var c = prefix.charAt(idx);
            var node = this.children.get(c);
            if (node == null) {
                return false;
            }
            if (prefix.length() == idx + 1) {
                return true;
            }
            return node.startsWith(prefix, idx + 1);
        }
    }
}
```

실행 결과: Accepted

- Runtime 46 ms, Beats 22.7%
- Memory 55.8 MB, Beats 5.94%

- 입력값은 모두 알파벳 소문자만 사용한다는 제약 조건을 활용한다.
- 각 노드가 미리 최대한의 자식 노드를 담을 수 있는 공간을 확보한다.
- 그러면 `Node` 의 내부 자료구조를 더 단순화시킬 수 있다.

```java
package trie;

class Trie {
    private final Node root;

    public Trie() {
        this.root = new Node();
    }

    public void insert(String word) {
        this.root.insert(word, 0);
    }

    public boolean search(String word) {
        return this.root.search(word, 0);
    }

    public boolean startsWith(String prefix) {
        return this.root.startsWith(prefix, 0);
    }

    private static final class Node {
        private final Node[] nodes;
        private boolean isEnd;

        public Node() {
            this.nodes = new Node[26];
        }

        public void insert(String word, int idx) {
            if (idx >= word.length()) {
                return;
            }

            var i = word.charAt(idx) - 'a';
            if (this.nodes[i] == null) {
                this.nodes[i] = new Node();
            }
            if (word.length() == idx + 1) {
                this.nodes[i].isEnd = true;
            }
            this.nodes[i].insert(word, idx + 1);
        }

        public boolean search(String word, int idx) {
            if (idx >= word.length()) {
                return false;
            }

            var node = this.nodes[word.charAt(idx) - 'a'];
            if (node == null) {
                return false;
            }
            if (word.length() == idx + 1 && node.isEnd) {
                return true;
            }
            return node.search(word, idx + 1);
        }

        public boolean startsWith(String prefix, int idx) {
            if (idx >= prefix.length()) {
                return false;
            }

            var node = this.nodes[prefix.charAt(idx) - 'a'];
            if (node == null) {
                return false;
            }
            if (prefix.length() == idx + 1) {
                return true;
            }
            return node.startsWith(prefix, idx + 1);
        }
    }
}
```

실행 결과: Accepted

- Runtime 31 ms, Beats 98.13%
- Memory 54.8 MB, Beats 55.39%
