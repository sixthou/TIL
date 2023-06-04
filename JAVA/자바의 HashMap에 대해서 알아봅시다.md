# 자바의 HashMap에 대해서 알아봅시다🧐

> 자바는 어떤 방식으로 HashMap 구현체의 성능을 향상시켰을까?
→ 할부 시간 복잡도를 위해 어떻게 해시 충돌 가능성을 줄였는지 자바7, 8을 기준으로 알아보자
> 

## HashMap이란?

HashMap은 ***키에 대한 해시 값을 사용해 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 연관배열*** 으로 정의할 수 있다. 

## 해시 분포와 해시 충돌

HashMap은 기본적으로 각 객체의 hashCode()메서드가 반환하는 값을 사용하는데 결과 자료형은 Int다.

```java
// Objects의 hash 메소드
public static int hash(Object... values) {
        return Arrays.hashCode(values);
}
```

논리적으로 생성 가능한 객체의 수는 $2^{32}$보다 많을 수 있고, 모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 $2^{32}$인 배열을 가지고 있어야 하기 때문에 완전한 해시 함수가 있더라도 사용할 수 있는것은 아니다. 

HashMap에서는 메모리르 절약하기 위해 함수 표현 정수 범위 $|N|$ 보다 작은 `M개`의 원소가 있는 배열을 사용한다. 따라서 해시 코드의 나머지 값을 해시 버킷 인덱스 값으로 사용한다. 

```java
int index = X.hashCode() % M;
```

M개의 버킷을 사용하다는 것은 1/M 확률로 같은 해시 버킷을 사용하게 되어 해시 충돌이 발생한다.

이런 해시 충돌이 발생해도 데이터를 잘 저장하고 조회할 수 있는 대표적 방법으로 `open adressing`과 `separate chaining` 기법이 있다.

### `open adressing` (선형 검색법)

!https://upload.wikimedia.org/wikipedia/commons/thumb/9/90/HASHTB12.svg/724px-HASHTB12.svg.png

- 데이터를 삽입하려는 해시 버킷이 이미 사용 중이면 다른 해시버킷에 해당 데이터를 삽입하는 방식이다.
- 데이터를 저장, 조회할 해시 버킷을 찾을 때는 선형 검색법, 2차 검색법 등을 사용한다.
- 연속된 공간 상용으로 캐시 효율이 높다.
- 데이터 개수가 적을때 비교적 성능이 좋다.
- 데이터 삭제에서 비효율적이다.

### `seperate chaining`

!https://upload.wikimedia.org/wikipedia/commons/thumb/d/d0/Hash_table_5_0_1_1_1_1_1_LL.svg/900px-Hash_table_5_0_1_1_1_1_1_LL.svg.png

- 각 배열의 인자는 인덱스가 같은 해시 버킷을 연결한 연결리스트의 첫 부분이다.
- 자바의 구현 기법
- 데이터가 많을 때 비교적 성능이 좋다.

### `open adressing` 과 `seperate chaining` 비교

`open adressing` 은 연속된 공간에 데이터를 저장하기 때문에 `seperate chaining` 보다 캐시 효율이 높다. 

데이터 개수가 충분히 적다면 `open adressing` 이 성능이 좋다. 배열의 크기가 커지면 `seperate chaining` 이 더 효율적이다. 

## 자바 8 HashMap에서의 `separate chaining`

자바 8에서는 이전 버전과 달리 `separate chaining` 에서 데이터의 개수가 많아지만 `트리`를 사용해 기대 호출값 $E(log\frac{N}{M})$을 보장한다. 하나의 해시 버킷에 할당된 키-값의 개수로 트리 사용 여부를 결정한다. 

```java
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
```

자바 8의 HashMap에서느 위처럼 상수 형태로 기준을 정해뒀다. 하나의 해시 버킷에 8개의 키-값 쌍이 모이면 트리로 변경하고, 데이터를 삭제해 갯수가 6개로 줄어들면 다시 연결 리스트로 변경한다. 8과 6 사이의 2의 차이를 둔것은 빈번한 변경으로 인한 성능 저하를 방지하기 위함이다. 

`red-black tree` 를 사용하고 트리 순회시 사용되는 대소 판단 기준은 해시 함수 값이다. 

실제 구현 코드를 살펴보자.

```java
// 처음 사용할 때 초기화 되고 필요에 따라 크기가 조정됨. 할달 길이는 항상 2의 거듭제곱근이다. 
transient Node<K,V>[] table;

static class Node<K,V> implements Map.Entry<K,V> {
// 자바7의 Entry 클래스와 구현 내용이 같다. 
}

// LinkedHashMap.Entry 클래스는 HashMap.Node를 상송한 클래스로 
// TreeNode 객체를 table 배열에 저장할 수 있다. 
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion

        boolean red;

        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }

        final TreeNode<K,V> root() {
   				// 트리 노드의 root를 반환한다. 
        }

        static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
   				// 해시 버킷에 트리를 저장할 떄는 root노드에 가장 먼저 접근해야 함
        }

				// 순회하며 트리 노드 조회
        final TreeNode<K,V> find(int h, Object k, Class<?> kc) {...}
        final TreeNode<K,V> getTreeNode(int h, Object k) {...}

        static int tieBreakOrder(Object a, Object b) {
	        // 트리 노드에서 어떤 두 키의 compare 값이 같으면 서로 동등하게 취급된다.
					// 해시 값이 서로 같아도 서로 동등하지 않을 수 있기 때문에
					// 해시 함수 값이 같을 경우 임의로 대소 관계를 지정해야한다.
        }

        final void treeify(Node<K,V>[] tab) {
          // 연결 리스트를 트리로 변환한다. 
        }

        final Node<K,V> untreeify(HashMap<K,V> map) {
					// 트리를 연결 리스트로 변환한다.
        }

        final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab,
                                       int h, K k, V v) {
					// 트리에 값을 추가한다. 
				}

        final void removeTreeNode(HashMap<K,V> map, Node<K,V>[] tab,
                                  boolean movable) {
					// 트리에 노드를 제거한다. 
				}
		
		    //red-black 구성 규칙에 따른 균형유지를 위한 메서드.    
        final void split(...)
        static <K,V> TreeNode<K,V> rotateLeft(...)
        static <K,V> TreeNode<K,V> rotateRight(...)
        static <K,V> TreeNode<K,V> balanceInsertion(...)
        static <K,V> TreeNode<K,V> balanceDeletion(...)

        static <K,V> boolean checkInvariants(TreeNode<K,V> t) {
				// 트리가 규칙에 맞게 생성됐는지 판단한다. 
        }
    }

```

## 해시 버킷의 동적 확장

해시 버킷의 개수가 적다면 메모리 사용을 아낄 수 있지만 해시 충돌로 성능 손실이 발생한다. → HashMap은 키-값 쌍 데이터 개수가 일정 개수 이상이면 해시 버킷의 개수를 두배로 늘린다.

해시 버킷 개수의 기본값은 16이고, 버킷의 최대 개수는 $2^{30}$이다.

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
static final int MAXIMUM_CAPACITY = 1 << 30;
```

버킷 개수가 두 배로 증가할 때마다 키-값 데이터를 읽어 새로운 `separate chaining` 을 구성해야하는 문제가 있다. 

해시 버킷 크기를 두배로 확장하는 임계정은 현재의 데이터 개수가 `부하율(0.75)` * `현재의 해시 버킷 개수` 에 이를 때이다. 부하율을 생성자에서 지정할 수도 있지만 기본값은 0.75이다. 

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
...

else {               // zero initial threshold signifies using defaults
    newCap = DEFAULT_INITIAL_CAPACITY;
    newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
}
```

## 보조 해시 함수

해시 버킷 크기를 두 배로 확장하는 것에는 결정적 문제가 있다. 해시 버킷의 개수 M이 $2^a$형태가 되기 때문에 index = X.hashCode() % M 을 계산할때 X.hashCode()의 하위 a 비트만 사용하게 된다는 것이다. 보조 해시 함수로 이를 해결할 수 있다. 

키의 해시 값을 변경해 해시 충돌을 줄이는 것이 보조 해시 함수의 목적이다. 

```java
//자바8의 보조 해시 함수
static final int hash(Object key) {
  int h;
  return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

상위 16비트 값을 XOR 연산한다. 

개념상  index = X.hashCode() % M 처럼 나머지 연산을 사용하는 것이 맞지만 M 값이 $2^a$일 때는 해시 함수의 하위 a비트만을 취하는것과 값이 같다. 따라서 나머지 연산 보다 비트 논리곱 연산을 사용하는게 빠르다.

## 정리

- HashMap은 `키-값` 형태로 데이터를 저장하는 자료구조이다.
- 데이터는 키의 해시값으로 부터 산출된 인덱스에 저장된다.
- O(1)의 성능을 보여준다.
- 데이터가 저장되면서 충돌이 발생하고 O(N)까지 성능이 떨어질 수 있는데 Tree를 사용해 성능을 개선한다.
