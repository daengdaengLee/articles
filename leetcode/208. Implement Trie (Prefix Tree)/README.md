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
