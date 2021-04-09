---
title: 선형 리스트
date: "2021-04-09"
description: "선형 리스트 정리 (JAVA)"
---

# 선형 리스트 (Linear List)

---

## 1. 개념

- 원소들이 나열된 논리적인 순서와 메모리에 저장되는 물리적인 순서가 일치하는 자료구조
- 배열과 동일하다! (정확히는 선형 순차 리스트라고 하는 것이 맞는 것 같다. 연결 리스트 역시 선형 리스트에 포함되는 것 같다.)

## 2. 구현

### 생성자

```java
class LinearList<T> {
	T[] list;
    int tail = 0;

    public LinearList (int size) {
        this.list = (T[]) new Object[size];
    }
}
```

일단, 배열과 동일하기 때문에 나름대로 구현을 해보았다. 직접 값을 저장하는 list와 배열의 끝을 지정하는 tail이라는 변수를 하나 두었다. (배열의 끝을 지정하고 있으면 맨 뒤에 원소 추가할 때, 또는 맨 뒤 원소를 제거할 때 편리해서 하나 만들었다.)

위와 같이 작성했는데, 참고로 T[]로 생성자는 사용할 수 없고, (생성자가 없는 클래스가 존재하기 때문인 것으로 보인다. 생각을 좀 더 해보니 인터페이스나 추상 클래스의 경우 역시 객체 생성이 불가능하니 당연히 오류가 뜰 것이다..!) Object[]를 T[]로 형변환하면 컴파일 경고가 뜬다. 이 부분에 대해서는 조금 더 조사가 필요할 것으로 보인다. 또한 오류가 뜨지 않게끔 구현하는 방법들도 존재하는 것 같다. 본 주제와는 조금 달라서, 조사 후 따로 작성하여 정리할 예정이다.

### 원소 추가 메서드 : append, add

```java
    public void append(int index, T value) {
        tail++;
        if (index >= tail) {
            add(value);
        } else {
            T[] tempList = (T[]) new Object[tail];
            tempList[index] = value;
            System.arraycopy(list, 0, tempList, 0, index);
            if (tail > index) {
                System.arraycopy(list, index, tempList, index + 1, tail - (index + 1));
            }
            this.list = tempList;
        }
    }

    public void add(T value) {
        if (tail < list.length) {
            this.list[this.tail++] = value;
        } else {
            T[] tempList = (T[]) new Object[++tail];
            System.arraycopy(list, 0, tempList, 0, tail - 1);
            tempList[tail - 1] = value;
            this.list = tempList;
        }
    }
```

꽤나 소스가 길어지는 것을 볼 수 있다. (심지어 자바에서 주는 배열 복사 메서드를 사용하지 않는다면 for문으로 더 길어진다.)

완벽하게 최적화했다고 보기는 어려우나, 공통적인 특징으로는 배열의 길이가 넘어가는 경우 새로운 배열을 생성해 줘야 한다는 점이다. 또한 append의 경우 중간에 데이터를 삽입하게 되면 값들을 뒤로 밀어주는 작업을 필요로 한다.

배열을 이런식으로 쓰지 않는 이유, 연결 리스트라는 자료구조를 사용하게 된 이유를 잘 보여주는 것으로 보인다.

(구현 후 글 작성하면서 생각이 난 부분인데, 배열은 접근 속도가 빠르기 때문에 (O(1)) 일부 자료구조에서는 위와 같이 삽입이 조금 비효율적일 수 있어도 사용하는 걸로 알고 있다. (예를 들면 해쉬테이블, ArrayList는 한 번 조사해봐야 할듯하다.) 그래서 배열 전체 길이의 75%정도? 를 차지하고 있다면 두 배 길이의 배열을 새로 만들어서 저장하는 방식을 사용하는 걸로 알고 있다. 한 번 바꿔서 구현해보는 것도 괜찮을 것 같다)

### 원소 제거 메서드 :  pop, remove

```java
    public T pop() {
        T result = list[tail - 1];
        list[tail - 1] = null;
        tail--;
        return result;
    }

    public void remove(T value) {
        for (int i = 0; i < tail; i++) {
            if (list[i].equals(value)) {
                for (int j = i; j < tail - 1; j++) {
                    list[j] = list[j + 1];
                }
                tail--;
                return;
            }
        }
        System.out.println("Not Found");
    }
```

pop은 보통 stack에서 구현하긴 하나 tail이라는 변수를 따로 만든 김에 같이 구현해봤다.

pop은 깔끔하게 구현되었는데, (지금 보니 예외 처리들을 안했다..) 핵심은 remove에서 역시 for문이 있다는 걸 확인할 수 있다. 중간 값이 제거된다면, 앞으로 원소들을 한 칸 씩 당겨와야 한다.



## 3. 결론!

- 배열의 중간에 값의 삽입, 삭제시 한 칸씩 뒤로 밀어주는 작업이 필요하다. (O(N)이라고 보면 될 것 같다)
- 또한, 사용하지 않더라도 일정 크기의 메모리를 잡아먹게 된다.
- index 접근이 가능하기 때문에, 탐색 속도는 빠르다. (O(1)) 이 장점으로 인해 위 단점에도 불구하고 많이 사용된다.



조금 부실하게 정리한 것 같아서, 추후 자바의 ArrayList가 어떻게 구현되어있는지 한 번 살펴보면서 다시 한 번 정리해봐야겠다.