# Swift 퍼포먼스 최적화의 기본: Value Type, Stack, Heap

## 개요 (Overview)
이 문서는 Swift에서 퍼포먼스를 최적화하는 기본 개념과 전략을 설명합니다. 특히 Value Type, Stack, Heap이 성능에 미친 영향을 중점적으로 다루는 방식을 다룰 것입니다.

## 문제점 (Problem)
Swift에서는 최적화되지 않은 코드가 성능 저하를 일으키는 원인이 될 수 있습니다. 예를 들어, Value Type와 Reference Type을 적절하게 사용하지 않으면 불필요한 메모리 복사나 참조 카운트 증가가 발생할 수 있습니다. Stack과 Heap 메모리 관리에 대한 이해 부족 외에도 앱의 응답 속도가 떨어지게 됩니다. 

## 해결 방법 (Solution)

### 1. Value Type과 Reference Type의 적절한 사용
- [Value Type](https://www.swift.org/documentation/articles/value-and-reference-types.html)(예: `struct`)은 Stack에 저장되어 빠른 접근이 가능합니다. 감지된 데이터를 여러 개 리케이션 없이 처리할 때 적합합니다.
- [Reference Type](https://www.swift.org/documentation/articles/value-and-reference-types.html)(예: `class`)은 Heap에 저장되며, 여러 인스턴스가 데이터를 공유해야 할 때 사용합니다.

### 2. [Copy-on-Write(COW) 활용](https://developer.apple.com/documentation/swift/unsafepointer?source=post_page-----1219164b6c41---------------------------------------)
- Swift의 `Array`, `Dictionary`는 Copy-on-Write를 지원해 불필요한 메모리 복사를 제어할 수 있습니다.

#### COW 예시 코드
```swift
var arrayA = [1, 2, 3]
var arrayB = arrayA  // arrayA와 arrayB는 메모리를 공유

print(ObjectIdentifier(arrayA as AnyObject))
print(ObjectIdentifier(arrayB as AnyObject))  // 같은 메모리 주소

arrayB.append(4)  // arrayB를 수정하는 순간 복사가 발생

print(arrayA)  // [1, 2, 3]
print(arrayB)  // [1, 2, 3, 4]
```
> `arrayB`를 수정할 때만 새로운 메모리가 할당되고, 그 전까지는 같은 메모리를 공유합니다.

### 3. Stack과 Heap 메모리 구조 이해
- Stack은 빠른 접근이 가능하지만 크기가 제한적입니다. 작은 데이터에 적합합니다.
- Heap은 더 큰 데이터를 저장할 수 있지만, 접근 속도가 느립니다.

### 4. ARC(Automatic Reference Counting) 최적화
- 순회 참조를 방지하려면 `weak`나 `unowned` 키워드를 적절히 사용해야 합니다.

#### ARC 예시 코드
```swift
class Person {
    var name: String
    var friend: Person?

    init(name: String) {
        self.name = name
        print("\(name) is initialized")
    }

    deinit {
        print("\(name) is deinitialized")
    }
}

var john: Person? = Person(name: "John")
var jane: Person? = Person(name: "Jane")

john?.friend = jane
jane?.friend = john 

john = nil
jane = nil  
```
> 두개의 인스턴스가 서로를 참조해서 메모리에서 사라지지 않습니다.

**순회 참조 해결 방법:**
```swift
class Person {
    var name: String
    weak var friend: Person? 

    init(name: String) {
        self.name = name
        print("\(name) is initialized")
    }

    deinit {
        print("\(name) is deinitialized")
    }
}
```
> `weak`을 사용하면 순회 참조를 방지하고, 개체가 정상적으로 메모리에서 해제됩니다.
