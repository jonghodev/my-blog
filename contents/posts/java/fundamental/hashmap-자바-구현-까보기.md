---
title: HashMap 자바 구현 까보기
date: 2021-09-13 21:00:00
update: 2021-09-13 21:00:00
tags:
 - java
description:
---

스타 컴까기 처럼 HashMap 을 까보자!

## 해시맵 구현에 사용된 자료구조

우선 해시맵이 어떤 자료구조로 구현되었는지 살펴보자.

여기서 table 이라는 필드가 해시맵 데이터들이 들어있는 곳이다.

table 의 타입을 보면 링크드 리스트 배열인 것을 볼 수 있다.

여기서 Node 는 링크드 리스트에서 사용되는 그 Node 처럼 구현되어 있다. 이 노드가 링크드 리스트에서 어떻게 사용되는지 감이 안 온다면.. 혹은 링크드 리스트를 구현해본 적이 없다면 C 언어 같은 것으로 링크드 리스트를 구현해보고 글을 읽는 것이 도움이 될 것 같다.

```java
transient Node<K,V>[] table;

static class Node<K,V> implements Map.Entry<K,V> {
	  final int hash;
	  final K key;
	  V value;
	  Node<K,V> next;

	  Node(int hash, K key, V value, Node<K,V> next) {
	      this.hash = hash;
	      this.key = key;
	      this.value = value;
	      this.next = next;
	  }
		...
}
```

아래에서 왜~ 해시맵이 링크드 리스트 배열을 사용하고, 왜 O(1) 의 속도가 나오는지 알아보자.

## 해시맵이 링크드 리스트 배열을 사용하는 이유

HashMap 은 배열의 Index 에 Key 값의 해시 값을 넣는다. 그래서 O(1) 의 속도가 나오는 것이다.

예제로 보자.

다음 코드는 "someKey" 라는 키 값으로 "someValue" 라는 Value 값을 넣는 작업이다.

```java
Map<String, Object> map = new HashMap<>();
map.put("someKey", "someValue");
```

"someKey" 문자열의 해시 값을 "HashXXX" 라고 가정하자. 그러면 해시맵 내부에서는 아래와 같은 값의 대입이 일어난다. 이때 테이블은 위에서 살펴봤던 해시맵의 모든 데이터를 저장하는 table 필드다!

```java
public void put() {
		// Node(hash, key, value, next)
		table = new Node("HashXXX", "someKey", "someValue", null);
}

public V get() {
		return table["HashXXX"]
}

```

> 실제로는 key값을 (hashing & n - 1)으로 계산해서 인덱스로 사용한다.

next 에 null 값을 넣을 것이고 사용을 안 할 것 같은데 왜 next 필드를 갖고 있는 링크드 리스트를 사용 했을까?

단순히 key value 만 갖고 있는 노드 객체를 사용해도 되는데 말이다!

그 이유를 알아보자.

### LinkedList 를 사용하는 이유

단순히 키 배열로 된 객체면 되는데 next 필드가 있는 링크드 리스트 객체를 사용하는 이유는 만약 **해시 충돌이 났을 경우 next 에 그 값을 넣어주기 위해서다.**

해시 충돌이란 서로 다른 키 값을 사용하는데 해시 값이 겹쳤을 때를 말한다.

이제 코드를 자세히 까볼 것인데, putVal 함수를 보다보면 해시 충돌이 났을 때 next 필드에 노드를 넣어주는 코드를 확인할 수 있을 것이다.

## put() 함수 까보기

```java
// HashMap 의 대표적인 함수인 put() 함수를 호출하면 putVal() 함수가 호출된다.
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

// 이 함수에서 데이터 삽입 로직을 처리한다.
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                 boolean evict) {
      Node<K,V>[] tab; Node<K,V> p; int n, i;
      // 처음 생성된 해시맵이거나 길이가 0인 해시맵이라면 resize() 한다.
      if ((tab = table) == null || (n = tab.length) == 0)
          n = (tab = resize()).length;
      // 해시맵에 이미 존재하는 Key 값인지 해시를 이용해 검색한다.
      // 만약 존재 하지 않는 해시값이라면 해시맵 테이블에 Value 를 넣어준다.
      if ((p = tab[i = (n - 1) & hash]) == null)
          tab[i] = newNode(hash, key, value, null);
			// 만약 존재하는 해시 값이라면, 키 값이 같아서 해시 값이 같은건지, 아니면 키 값이 다른데
      // 해시 값이 같아서 해시 충돌이 난건지를 아래에서 구분할 것이다.
      else {
          Node<K,V> e; K k;
					// 같은 키 값을 사용한 경우다.
          // 테이블에 변화를 줄 필요는 없고 put 함수의 리턴 값으로 중복된 value 를 다시 넘겨준다.
          if (p.hash == hash &&
              ((k = p.key) == key || (key != null && key.equals(k))))
              e = p;
          // TreeNode 일 경우. 아직 확인 안 해본 코드.
          else if (p instanceof TreeNode)
              e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
					// 해시 충돌이 난 경우: 다른 키 값을 사용했는데 해시가 겹침.
          else {
              for (int binCount = 0; ; ++binCount) {
									// 해당 키값으로 처음 해시 충돌이 난 경우라면 next 에 그 Value 를 넣어준다.
                  if ((e = p.next) == null) {
                      p.next = newNode(hash, key, value, null);
                      if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                          treeifyBin(tab, hash);
                      break;
                  }
									// 해시 충돌이 나서 이미 해시맵에 들어갔던 Value 라면 Break 해서 코드 탈출
                  if (e.hash == hash &&
                      ((k = e.key) == key || (key != null && key.equals(k))))
                      break;
                  p = e;
              }
          }
					// 값은 키 값을 사용해서 해시가 같았던 경우다. 중복된 value 값을 반환하는 코드.
          if (e != null) { // existing mapping for key
              V oldValue = e.value;
              if (!onlyIfAbsent || oldValue == null)
                  e.value = value;
							// 이벤트 콜백성 코드
              afterNodeAccess(e);
              return oldValue;
          }
      }
      ++modCount;
      if (++size > threshold)
          resize();
			// 이벤트 콜백성 코드
      afterNodeInsertion(evict);
      return null;
  }
```

## get() 함수 까보기

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(key)) == null ? null : e.value;
}

/**
 * Implements Map.get and related methods.
 *
 * @param key the key
 * @return the node, or null if none
 */
final Node<K,V> getNode(Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n, hash; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & (hash = hash(key))]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
				// next 필드가 존재 한다는 것은 해시 충돌이 났었다는 것이다. 해시 충돌이 난 value 를 반환.
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

## resize() 함수 까보기

해시맵 버킷 사이즈가 일정 수준으로 차면 배열의 크기를 증가시킨다. 보통 두 배로 확장한다. 확장하는 임계점은 75%이다. 코드에서도 확인 해보면 `static final float DEFAULT_LOAD_FACTOR = 0.75f;` 와 같이 구현되어 있다. 리사이징은 더 큰 버킷을 가지는 새로운 배열을 만들고 거기에 hash 를 다시 계산해서 복사해준다.

```java
final Node<K,V>[] resize() {
      Node<K,V>[] oldTab = table;
      int oldCap = (oldTab == null) ? 0 : oldTab.length;
      int oldThr = threshold;
      int newCap, newThr = 0;
      if (oldCap > 0) {
					// 이 코드에서 HashMap 의 맥스 사이즈를 체크한다.
					// MAXIMUM_CAPACITY = 1 << 30 = 1073741824 를 넘는지 검사해서 넘는다면
					// threshold 값을 MAX Integer 로 설정한다.
          if (oldCap >= MAXIMUM_CAPACITY) {
              threshold = Integer.MAX_VALUE;
              return oldTab;
          }
          else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                   oldCap >= DEFAULT_INITIAL_CAPACITY)
              newThr = oldThr << 1; // double threshold
      }
      else if (oldThr > 0) // initial capacity was placed in threshold
          newCap = oldThr;
			// 해시맵 처음 Default 셋팅!
			// Default bucket의 수 : 16, loadFactor = 0.75f, threshold = 12
      else {               // zero initial threshold signifies using defaults
          newCap = DEFAULT_INITIAL_CAPACITY;
          newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
      }
      if (newThr == 0) {
          float ft = (float)newCap * loadFactor;
          newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                    (int)ft : Integer.MAX_VALUE);
      }
      threshold = newThr;
      @SuppressWarnings({"rawtypes","unchecked"})
      Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
      table = newTab;
      if (oldTab != null) {
          for (int j = 0; j < oldCap; ++j) {
              Node<K,V> e;
              if ((e = oldTab[j]) != null) {
                  oldTab[j] = null;
                  if (e.next == null)
                      newTab[e.hash & (newCap - 1)] = e;
                  else if (e instanceof TreeNode)
                      ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                  else { // preserve order
                      Node<K,V> loHead = null, loTail = null;
                      Node<K,V> hiHead = null, hiTail = null;
                      Node<K,V> next;
                      do {
                          next = e.next;
                          if ((e.hash & oldCap) == 0) {
                              if (loTail == null)
                                  loHead = e;
                              else
                                  loTail.next = e;
                              loTail = e;
                          }
                          else {
                              if (hiTail == null)
                                  hiHead = e;
                              else
                                  hiTail.next = e;
                              hiTail = e;
                          }
                      } while ((e = next) != null);
                      if (loTail != null) {
                          loTail.next = null;
                          newTab[j] = loHead;
                      }
                      if (hiTail != null) {
                          hiTail.next = null;
                          newTab[j + oldCap] = hiHead;
                      }
                  }
              }
          }
      }
      return newTab;
  }
```

## 해시 맵의 성능

탐색, 삽입, 삭제 모두 평균적인 경우 O(1), 최악의 경우 O(N) 이다. 무턱대고 O(1) 라고 대답하면 안 된다. 이유는 해시 충돌이 발생할 수 있기 때문이다.

## Reference

https://sabarada.tistory.com/57

https://hee96-story.tistory.com/48
