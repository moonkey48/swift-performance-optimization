# Dictionary와 Array의 CRUD 시간복잡도 이해하기

- Dictionary와 Array 각각의 CRUD(생성, 읽기, 수정, 삭제) 연산에 필요한 시간복잡도를 이해할 수 있습니다.
- 상황에 적절한 데이터 구조를 선택할 수 있습니다. 


## Dictionary의 CRUD 시간복잡도

- **Create (삽입)** 
    
  평균: O(1) / 최악: O(N)
  
  > 삽입을 하는데 드는 시간이 평균적으로 상수시간이기 떄문에 key를 기반으로 값에 접근해야 하는 경우에 Dictionary를 사용할 수 있습니다.
  ```swift 
  let dict = [String:Int]()
  dict["moonkey"] = 0 
  ```
- **Read (읽기)** 

    평균: O(1) / 최악: O(N)
    > 삽입과 마찬가지로 상수시간에 읽기가 가능합니다. 배열과 다르게 key에 대한 값이 없을 수 있어서 Optional타입으로 값을 가져올 수 있습니다. 
  ```swift 
  if let value = dict["moonkey"] {
    ...
  }
  ```
- **Update (수정)**
  
   평균: O(1) / 최악: O(N)

   > 이미 만든 값에 접근할 때에는 배열과 다르게 바로 값을 추가할 수 없습니다(값이 없을 수 있기 때문에). 그래서 Unwrapping을 하거나 Swift가 제공해주는 default 기능을 사용해서 update를 할 수 있습니다.
  ```swift 
  dict["moonkey"] += 1 // Error

  if let count = dict["moonkey"] {
    dict["moonkey"] = count + 1
  } else {
    dict["moonkey"] = 1
  }

  // 또는
  dict["moonkey", default: 0] += 1
  ```
- **Delete (삭제)**

    O(1) 평균 / O(N) 최악
  > 기본적으로 아래와 같이 key에 대해서 `nil`을 할당하게 되면 제거가 이루어지게 됩니다.  
  또는 애플에서 제공하고 있는 `removevalue(forkey:)`을 통해서도 제거가 가능합니다. 하지만 이 함수는 O(n)의 시강복잡도를 가지기 때문에 왜 애플에서 상수의 시간 복잡도를 두고 이 함수를 만들었는지 알아봐야겠습니다.  
  ```swift 
  dict["moonkey"] = nil

  if let value = hues.removeValue(forKey: "moonkey") {
    print("The value \(value) was removed.")
    } else {
    print("No value found for that key.")}
  ```
    [removeValue(forKey:) 공식 문서](https://developer.apple.com/documentation/swift/dictionary/removevalue(forkey:))

<br>

**최악의 경우가 발생하는 이유**

Dictionary는 내부적으로 해시 테이블을 사용합니다. 해시 함수를 통해 데이터를 바로 접근할 수 있기 때문에, 평균적으로 매우 빠른 속도를 자랑합니다. 다만, 해시 충돌이 심각할 경우 최악의 경우에는 모든 요소를 탐색해야 하므로 O(N)까지 악화될 수 있습니다.





