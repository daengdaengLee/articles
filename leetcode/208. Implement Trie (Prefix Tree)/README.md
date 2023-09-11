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
