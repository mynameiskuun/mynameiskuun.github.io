---
title: "Effective Java - Exception"
categories:
  - book-study
tags:
  - effective-java
toc: true
toc_sticky: true
---

# 10장. 예외(Exception)

![alt text](/assets/images/Exception.png)

| Feature        | Checked Exception                                      | Unchecked Exception                                                             |
| -------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------- |
| 강제성         | 반드시 처리해야 함 (컴파일러가 검사)                   | 처리하지 않아도 됨 (컴파일러가 검사하지 않음)                                   |
| 발생 시점      | 컴파일 타임                                            | 런타임                                                                          |
| 예시           | `IOException`, `SQLException`, `FileNotFoundException` | `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException` |
| 주요 사용 사례 | 외부 자원 접근 (파일, 데이터베이스 등)                 | 프로그래머의 실수로 인한 논리적 오류                                            |
| 처리 방법      | `try-catch` 블록 사용 또는 메서드에 `throws` 선언      | 선택적 처리, 필요에 따라 `try-catch` 블록 사용                                  |
| 사용 시점      | 호출하는 쪽에서 복구하리라 여겨지면                    | 프로그래밍 오류를 나타낼 때                                                     |

---

## Item 69. 예외는 진짜 예외 상황에만 사용하라

```java
try{
    int i=0;
    while(true) {
        range[i++].climb();
    }
} catch(ArrayIndexOutOfBoundsException e) {

}
```

- 위 코드가 좋지 않은 코드인 이유는
  - 무한루프를 돌다가, 배열의 끝을 넘어 ArrayIndexOutOfBoundsException 발생 시 루프를 종료 시키고 있다.
  - 예외 처리를 예외 상황에서 사용하지 않고, 비즈니스 로직 제어용으로 사용하고 있다.

## 번외. ConcurrentModificationException

```java
List<String> collection = new ArrayList<>();

for(Iterator<Foo> it = collection.iterator(); i.hasNext();) {
    String a = i.next();
    if(a.equals("A")) {
        collection.remove(a); // 1
        i.remove(); // 2
    }
}
```

- 반복문을 이렇게 Iterator 객체를 사용해서도 작성할 수 있다.

> 상대적으로 어떤 장점을 갖는지 ChatGPT에게 물어보던 중, Iterator 사용 시 ConcurrentModificationException를 피할 수 있다는 것을 알게 되었다.

#### ConcurrentModificationException 에러 발생 이유

- 향상된 for loop은 내부적으로 Iterator를 사용하여 컬렉션을 순회한다.
- Iterator는 컬렉션을 순회하는 동안 컬렉션의 구조적 변경을 감지하기 위해, Iterator 생성 시 expectedModCount 변수 값을 collection 객체의 modCount값과 동일하게 설정 한다.
- 그리고 Iterator는 next() 호출 시 expectedModCount / modCount 값을 비교 후 다를경우 ConcurrentModificationException 에러를 발생시킨다.

- Iterator.remove()는 expectedModCount값을 정상적으로 갱신 시키지만, collection.remove()는 이 값을 갱신시키지 않아 상기 예외가 발생한다.

> 컬렉션 순회 중 컬렉션 내부를 수정 할 경우, 향상된 for문이 아닌 index로 접근하여 수정 혹은 Iterator를 사용하여 수정한다면 안전하게 수정이 가능하다.

---

## Item 70. 복구할 수 있는 상황에는 검사예외를, 프로그래밍 오류에는 런타임 예외를 사용하라.

- 프로그램의 안정성은 Unchecked Exception을 얼마나 잘 예측하고 관리하느냐에 달려있다. 추상화된 상위 Exception 객체를 사용하여 처리하기 보다는, 세분화된 하위 객체를 사용하여 개별 예외를 처리하자.

- 통상적으로 Unchecked Exception은 잡을 필요가 없거나, 잡지 말아야 한다.
  - 프로그램을 더 실행해봐야 득보다 실이 더 많다는 뜻이니, 적절한 메시지를 내뱉으며 해당 Thread를 중단 시켜야 한다.

---

## Item 71. 필요 없는 검사 예외 사용은 피하라

- Checked Exception의 남용은 쓰기 고통스러운 API를 낳는다. API 호출부에서 예외상황을 복구 할 방법이 없다면 Unchecked Exception을 던지자.
  - 복구가 가능하고 호출자가 그 처리를 해주길 원한다면, Optional을 반환하는것을 우선 고민하자.

---

## Item 72. 표준 예외를 사용하라.

| 예외                              | 주요 쓰임                                                                      |
| --------------------------------- | ------------------------------------------------------------------------------ |
| `IllegalArgumentException`        | 호출자가 인수로 부적절한 값을 넘길 때 던지는 예외                              |
| `IllegalStateException`           | 대상 객체의 상태가 호출된 메서드를 수행하기에 적절하지 않을 때                 |
| `NullPointerException`            | null 값을 허용하지 않는 메서드에 null을 건낼 때                                |
| `IndexOutOfBoundsException`       | 시퀀스의 허용 범위를 넘을 때                                                   |
| `ConcurrentModificationException` | 단일 스레드에서 사용하려고 설계한 객체를 여러 스레드가 동시에 수정하려고 할 때 |
| `UnsupportedOperationException`   | 클라이언트가 요청한 동작을 대상 객체가 지원하지 않을 때                        |

---

## Item 73. 추상화 수준에 맞는 예외를 던져라

- 상위 계층에서는 저수준 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔 던져야 한다.

```java
// List 객체의 get() 메소드

public E get(int index) {
    ListIterator<E> i = listIterator(index);

    try{
        return i.next();
    } catch(NoSuchElementException e) {
        throw new IndexOutOfBoundsException("index : " + index);
    }
}
```

- 예외 연쇄 : 문제의 근본 원인인 저수준 예외를 고수준 예외에 실어 보내는 방식.
  - 저수준 예외가 디버깅에 도움이 될 경우 사용한다.

```java

// 선언부
public String processFile(String filePath) {

    try {
        return fileReaderUtil.readFile(filePath);
    } catch (FileNotFoundException e) {
        throw new FileProcessingException("The specified file was not found: " + filePath, e);
    } catch (IOException e) {
        throw new FileProcessingException("An error occurred while reading the file: " + filePath, e);
    }
}

// 호출부
public static void main(String[] args) {

    ...

    try {
        obj.processFile("nonexistentfile.txt");
    } catch (FileProcessingException e) {
        System.err.println("Failed to process file: " + e.getMessage());
        e.printStackTrace();
    }
}

```

---

## Item 75. 예외의 상세 메시지에 실패 관련 정보를 담아라.

- 실패 순간을 포착하려면 발생한 예외에 관련된 모든 매개변수와 필드의 값을 실패 메시지에 담아야 한다.

```java
throw new IndexOutOfBoundsException(String.format("min : %d, max : %d, index : %d", minVal, maxVal, index));
```
