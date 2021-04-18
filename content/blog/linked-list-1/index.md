---
title: 연결 리스트
date: "2021-04-18"
description: "연결 리스트 정리 (JAVA)"
---

# 연결 리스트 (Linked List)

---

## 1. 개념

- 원소들이 논리적인 순서로 나열된 자료구조 (물리적 순서는 상관 없다)

- 노드 단위로 메모리 할당, 값과 다음 노드의 주소를 저장하는 것으로 구현한다.

    

- 다양한 연결 리스트가 있음. (단방향, 양방향, 원형 등)

- 다른 자료구조에서도 많이 활용됨. 



## 2. 구현

### 기본 구조

```java
public class LinkedList<T> {
    Node<T> head = new Node<>();
    Node<T> tail = head;
    int length = 0;
    
    static class Node<T> {
        T t;
        Node<T> next;
    }
}
```

- Node 클래스에는 값과 다음 노드의 주소가 저장된다.
- LinkedList 클래스를 따로 두어, 첫 번째 노드 (head) 를 이용해 삽입, 탐색 등의 연산을 한다.

- tail과 length 같은 멤버는필요하진 않다. 원래 연결 리스트의 삽입 연산, 삭제 연산은 O(N)인데 (순회를 거쳐야 하기 때문) stack과 비슷한 연산이 가능하게끔 tail 노드를 두어 좀 더 빠른 연산을 생각해 보았다.
    결과적으로 위 장점과 다른 연산시에 tail과 length를 수정해주어야하는 번거로움, 코드의 복잡성이 중가하였다.



### 1. 삽입

```java
    public void add(T t) {
        System.out.println("[ADD] " + t);
        Node<T> node = new Node<>();
        node.value = t;

        tail.next = node;
        tail = node;
        length++;
    }
```

- void add(T t) : 리스트의 맨 뒤에 원소 삽입
    - 추가할 노드를 생성 및 값 설정
    - 현재 리스트의 tail이 가리키는 node의 next를 추가할 노드로 지정



```java
    public void insert(T t, int index) {
        if (index > length) {
            System.out.println("[FAILED] INDEX OUT OF BOUNDS");
            return;
        }
        System.out.println("[INSERT] " + t + " / " + index);

        Node<T> target = head;
        for (int i = 0; i < index; i++) {
            target = target.next;
        }

        Node<T> node = new Node<>();
        node.value = t;
        node.next = target.next;

        target.next = node;
        if (target == tail) {
            tail = node;
        }

        length++;
    }
```

- void insert(T t, int index) : 리스트에 삽입할 원소와 index를 같이 지정
    - 길이 체크 후, index가 길이 이상이면 error
        - length가 없는 기본적인 연결 리스트인 경우 순회 후, index에 도달하지 못하면 error
    - index에 해당하는 노드를 찾음
    - 생성한 노드의 next에 찾은 노드의 next를 저장, 찾은 노드의 next를 노드로 저장
        - (찾은 노드) -> (생성한 노드) -> (찾은 노드.next)
    - 만약 찾은 노드가 tail이면? tail을 생성한 노드로 변경



### 2. 삭제

```java
	public void remove(T t) {
        System.out.println("[REMOVE] " + t);
        Node<T> target = head;

        while (target.next != null) {
            if (t.equals(target.next.value)) {
                length--;
                target.next = target.next.next;
                if (target.next == null) {
                    tail = target;
                }
                return;
            }
            target = target.next;
        }
    }
```

- void remove(T t) : 리스트에서 원소를 찾아서 삭제
    - while문으로 list 끝까지 탐색
        - 첫 탐색 노드가 head이기 때문에 target.next부터 탐색
    - 탐색 중 원소를 찾는다면 길이 감소 후, 삭제처리
        - target.next가 삭제되므로 target.next를 target.next.next로 변경
            - target.next가 null인 경우는 없음 -> while 조건에 의해
        - target.next.next가 null이면 -> target.next는 tail이므로 tail을 변경



```java
	public T pop() {
        Node<T> target = head;
        while (target.next != tail) {
            target = target.next;
        }

        T result = target.next.value;
        tail = target;
        target.next = null;
        System.out.println("[POP   ] " + result);
        length--;
        return result;
    }
```

- T pop() : 리스트 끝 요소를 삭제 후 반환
    - target.next가 tail인 노드 탐색
        - tail 노드가 변경되기 때문에 target.next가 tail인 노드를 탐색
    - target.next.value 반환 후, target.next 노드는 삭제 처리
- 보통 pop() 연산의 경우 O(1)로 처리할텐데 단방향 연결리스트 및 tail이 끝 노드를 가리키므로 탐색과 함께 O(N)으로 처리해야 되었음.
    - tail을 끝 노드의 전 노드를 가리키게끔 설계를 변경하거나, 양방향 연결 리스트로 처리하는 경우 O(1)로 처리가 가능해질 것으로 예상함



### 3. 읽기

```java
    public void get(int index) {
        int copyIndex = index;
        Node<T> target = head;
        while (target.next != null) {
            if (index <= 0) {
                System.out.println("[GET   ] INDEX " + copyIndex + " FOUND " + target.next.value);
                return;
            }
            target = target.next;
            index--;
        }
        System.out.println("[FAILED] INDEX " + copyIndex + " OUT OF BOUNDS");
    }
```

- void get(int index) : 특정 index의 노드를 가져옴
    - index가 0이 될 때 까지 list 탐색
    - 만약 index가 0이 되면 해당 노드 반환
    - 만약 index가 0 이상인데도, 리스트를 모두 탐색했다면
        - 찾는 index가 범위 밖이므로 탐색 실패



```java
    public String toString() {
        StringBuilder sb = new StringBuilder();

        Node<T> target = head.next;
        sb.append("[");
        while (target.next != null) {
            sb.append(target.value)
                    .append(", ");
            target = target.next;
        }
        sb.append(target.value);
        sb.append("]");

        return sb.toString();
    }
```

- String toString() // 전체 순회, 문자열 연산
    - 여러 번의 문자열을 더하는 연산이 이루어지기 때문에 StringBuilder를 사용했다.
    - list 전체 탐색 및 각 노드의 value를 문자열에 더해서 출력



## 3. 결론

- 삽입 : O(1)
- 탐색, 삭제 : O(N)
- 배열로 구현한 리스트와 달리 삽입, 삭제시 노드의 next값만 변경해주면 해결됨. 즉, 새로운 배열을 생성하거나 배열 요소를 한 칸씩 미는 등의 복잡한 작업이 필요하지 않음
- 원소 개수만큼만의 메모리를 잡아먹게 됨

추후 자바의 LinkedList와 비교해보며 자바의 컬렉션이 어떻게 최적화되어있는지를 알아 보면 좋을 것 같다.

