# Properties

https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties

**값을 class/structure/enumeration 과 연관 짓는 것.**

* stored property : 인스턴스의 일부로서 상수/변수 값을 저장함. 클래스, 구조체에서만 사용 가능
* computed property : 저아보다는 값을 계산함. 클래스, 구조체, 열거형에서 모두 사용 가능.

Type property : Stored/computed property 와 다르게 타입 자체와 연관이 있는 타입 (저장, 연산 프로퍼티는 인스턴스와 관련 있음)

## Stored Properties

**클래스, 구조체의 인스턴스의 일부로서 저장된 상수/변수**

* 변수 저장 프로퍼티 : `var` 키워드 사용
* 상수 저장 프로퍼티 : `let` 키워드 사용

이니셜라이저에서 저장 프로퍼티의 기본값 지정 가능. 상수 저장 프로퍼티도 마찬가지.

```swift
struct FixedLengthRange {
    var firstValue: Int // 변수 저장 프로퍼티
    let length: Int // 상수 저장 프로퍼티
}

var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3) // 초기화 시점에 저장 프로퍼티들이 초기화됨
rangeOfThreeItems.firstValue = 6 // 변수 저장 프로퍼티는 초기화 시점 이후에 수정될 수 있지만 상수 저장 프로퍼티는 못함
```

### 상수 struct 인스턴스의 저장 프로퍼티

* **구조체는 Value type** : 인스턴스를 상수로 만들면 인스턴스의 프로퍼티가 변수 저장 프로퍼티여도 수정할 수 없음.
* **클래스는 reference 타입** : 인스턴스를 상수로 만들어도 프로퍼티 수정 가능

```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
rangeOfFourItems.firstValue = 6 // 컴파일 에러. struct 가 let 인 경우 프로퍼티 수정 불가
```

### Lazy Stored Properties

**처음 사용되기 전까지 초깃값이 계산되지 않는 프로퍼티.** `lazy` modifier 를 붙여서 명시함.

**`lazy` 프로퍼티는 항상 `var` 로 변수로 선언해야 함. 그 이유는 인스턴스의 초기화가 끝난 후에도 프로퍼티의 초깃값이 할당되지 않을 수 있기 때문.** 상수 프로퍼티는 항상 초기화 이전에 값을 갖고 있어야 하기 때문에 lazy 할 수 없음.

* 유용한 경우
  * 외부 요소에 영향을 받아서 인스턴스의 초기화가 끝난 후에도 초깃값이 미정일 수 있는 프로퍼티가 있을 때
  * 프로퍼티의 초깃값이 복잡한 연산이나 설정을 필요로 해서 프로퍼티가 필요하기 전까지 굳이 실행될 필요가 없는 경우

```swift
class DataImporter {
    var filename = "data.txt"

    // 초기화하는데 일정 시간이 필요하다고 가정
}

class DataManager {
    lazy var importer = DataImporter() // 매니저가 import 없이도 데이터 관리를 할 수 있음
    var data: [String] = []
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// manager.importer 가 접근되지 않았기 때문에 DataImporter 인스턴스가 생성되지 않음.

print(manager.importer.filename) // 이 때 DataImporter 인스턴스가 생성됨
```

**`lazy` property 가 여러개의 쓰레드에서 동기에 접근되고 프로퍼티가 아직 초기화되지 않았을 때 프로퍼티가 오직 한 번만 초기화된다는 보장은 없음.**

### Stored Properties and Instance Variables

Obj-C 를 접했다면 클래스의 인스턴스의 일부로 값/참조를 저장하는 두 가지 방법이 있음을 알고 있을 것. 프로퍼티에 추가로, instance variable 을 프로퍼티에 저장된 값들을 위한 backing store 로 사용할 수 있음.

Swift 는 이를 하나의 프로퍼티 정의로 합침. Swift 프로퍼티는 대응되는 instance variable 이 없으며, 프로퍼티의 backing store 이 직접적으로 접근되지 않음. 이런 접근법은 여러 다른 상황에서 값들이 어떻게 접근되는지에 대한 혼란을 막고 프로퍼티의 정의를 하나로 결정지어 버림. 프로퍼티에 대한 모든 정보(이름, 타입, 메모리 관리 특징) 은 타입의 정의의 일부로서 분산되지 않고 한 곳에 정의되어 있음.

## Computed Properties

**실제로 값을 저장하지 않는 프로퍼티. Class, struct, enum 에서 사용할 수 있음. 다른 프로퍼티의 값을 간접적으로 가져오고 설정하기 위한 getter, 옵셔널 setter 를 제공함.** 

```swift
struct Point {
    var x = 0.0, y = 0.0
}

struct Size {
    var width = 0.0, height = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
    // 연산 프로퍼티. 직접 Point 값을 저장하지 않음. 
    // getter, setter 를 사용해서 center 가 실제 저장 프로퍼티인 것처럼 만듦
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)

            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}

var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center // center 의 getter 가 실행됨
square.center = Point(x: 15.0, y: 15.0) // center 의 setter 가 실행됨 
```

### Shorthand Setter Declaration

연산 프로퍼티의 setter 가 "new value" 에 대한 이름을 정의하지 않으면 기본 이름인 `newValue` 가 사용됨.

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)

            return Point(x: centerX, y: centerY)
        }
        set {
            // 자동으로 newValue 라는 이름이 사용됨
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### Shorthand Getter Declaration

Getter 내에 한 문장밖에 없을 때 암묵적으로 그 표현을 리턴함. 함수에서 암묵적인 return 룰이 똑같이 적용됨.

```swift
struct CompactRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            // getter 내 한 문장은 암묵적으로 리턴
            Point(x: origin.x + (size.width / 2), 
                        y: origin.y + (size.height / 2))
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### 읽기 전용 Computed Properties

**Getter 만 있고 setter 는 없는 연산 프로퍼티**. 

**연산 프로퍼티 (읽기 전용 포함)는 `var` 로 변수 프로퍼티로 설정해야 함. 그 이유는 값이 고정되지 않았기 때문. `let` 키워드는 값이 인스턴스의 초기화에서 설정된 후로 변경될 수 없음을 명시하기 위해 사용.**

아래와 같이 읽기 전용 연산 프로퍼티에 `get` 키워드와 괄호를 없애서 간단하게 정의 가능.

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0

    // 읽기 전용 연산 프로퍼티
    var volume: Double {
        return width * height * depth
    }
}
```

## Property Observers

**프로퍼티의 값이 변함을 관찰하고 대응함.** 프로퍼티의 값이 설정될 때마다 (이전 값과 같은 값이더라도) 호출됨.

* Property observer 가 추가될 수 있는 곳
  * 정의한 stored property
  * 상속받은 stored property
  * 상속받은 computed property

상속받은 프로퍼티의 경우 subclass 내의 프로퍼티를 overriding 해서 property observer 를 추가함. 직접 정의한 연산 프로퍼티의 경우 프로퍼티의 setter 를 사용해서 값이 변하는 것에 대응함.

* `willSet` : 값이 저장되기 전에 호출됨. 새로운 값이 상수 파라미터(`newValue`)로 전달됨. 
* `didSet` : 새로운 값이 저장된 직후 호출됨. 이전 값이 상수 파라미터(`oldValue`)로 전달됨.

Superclass 프로퍼티의 `willSet`, `didSet` observer 들은 subclass 이니셜라이저에서 프로퍼티가 설정될 때, superclass 이니셜라이저가 호출된 후에 실행됨. Subclass 에서 자신의 프로퍼티를 설정하고 있는 도중, superclass 이니셜라이저가 호출되기 이전에는 호출되지 않음.

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }

        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}

let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

Observer 가 있는 프로퍼티를 함수의 in-out 파라미터로 전달할 경우 `willSet`, `didSet` observer 들이 항상 호출됨. 그 이유는 in-out 파라미터에 대한 copy-in copy-out 메모리 모델 때문. 값은 항상 함수 끝부분에서 프로퍼티에 다시 쓰여짐.

## Property Wrappers

**프로퍼티가 어떻게 저장되는지 관리하는 코드와 프로퍼티를 정의하는 코드를 분리하는 레이어를 추가함.** 

e.g. 데이터베이스에 저장하기 위한 데이터에 쓰레드-안전 체크를 제공하는 프로퍼티의 경우 모든 프로퍼티에 해당 코드를 작성해야 함. Property wrapper 를 사용해서 wrapper 를 정의할 때 관리 코드를 한 번만 작성하고, 여러 프로퍼티들에 해당 코드를 재사용하기만 하면 됨.

Property wrapper 를 정의하기 위해서는 `wrappedValue` 프로퍼티를 정의하는 struct/enum/class 를 생성함.

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```

프로퍼티에 wrapper 를 적용하기 위해서는 프로퍼티 이름 전에 wrapper 이름을 attribute 로 적어주면 됨.

```swift
struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height)
// Prints "0"

rectangle.height = 10
print(rectangle.height)
// Prints "10"

rectangle.height = 24
print(rectangle.height)
// Prints "12"
```

프로퍼티에 wrapper 를 적용할 때, 컴파일러는 wrapper 를 위한 저장소를 제공하는 코드와 wrapper 를 통해 프로퍼티에 접근할 수 있게 하는 코드를 만들어냄. Attribute 문법을 사용하지 않고서도 property wrapper 와 같은 동작을 하는 코드를 작성할 수도 있음.

```swift
struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()

    var height: Int {
        get { return _height }
        set { _height.wrappedValue = newValue }
    }

    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```

### Setting Initial Values for Wrapped Properties

위 코드에서 `TwelveOrLess` 의 정의부분에 `number`에 초깃값이 이미 할당되어 있었기 때문에 다른 초깃값을 할당할 수 없음. 다른 초깃값을 할당하기 위해 property wrapper 에 이니셜라이저를 추가해야 함.

```swift
@propertyWrapper
struct SmallNumber {
    private var maximum: Int
    private var number: Int

    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, maximum) }
    }

    init() {
        maximum = 12
        number = 0
    }

    init(wrappedValue: Int) {
        maximum = 12
        number = min(wrappedValue, maximum)
    }

    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = min(wrappedValue, maximum)
    }
}
```

프로퍼티에 wrapper 를 적용하고 초깃값을 명시하지 않은 경우 `init()` 이니셜라이저 사용

```swift
struct ZeroRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int
}

var zeroRectangle = ZeroRectangle()
print(zeroRectangle.height, zeroRectangle.width)
// Prints "0 0"
```

프로퍼티에 초깃값을 명시하면 `init(wrappedValue:)` 이니셜라이저를 사용함.

```swift
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}

var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width)
// Prints "1 1"
```

커스텀 attribute 다음에 argument 를 작성하면 해당 인자를 받는 wrapper 의 이니셜라이저를 사용함.

```swift
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}

var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width)
// Prints "2 3"

narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width)
// Prints "5 4"
```

Property wrapper 인자를 포함시킬 때 할당문을 사용해서 초깃값을 지정할 수도 있음. Swift 는 할당문을 `wrappedValue` 인자로 취급하며 인자로 포함시킨 이니셜라이저를 사용함.

```swift
struct MixedRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber(maximum: 9) var width: Int = 2 
}

var mixedRectangle = MixedRectangle()
print(mixedRectangle.height)
// Prints "1"


mixedRectangle.width = 20
print(mixedRectangle.width)
// Prints "9"
```

### Projecting a Value From a Property Wrapper

**Property wrapper 가 *projected value* 를 정의해서 추가적인 기능을 제공할 수 있음.** Projected value 의 이름은 wrapped value 와 같은데 달러 기호로 시작함. 코드가 `$` 로 시작하는 프로퍼티를 정의할 수 없기 때문에 임의로 정의하는 프로퍼티와 절대로 충돌할 일이 없음.

```swift
@propertyWrapper
struct SmallNumber {
    private var number: Int
    private(set) var projectedValue: Bool

    var wrappedValue: Int {
        get { number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }

    init() {
        self.number = 0
        self.projectedValue = false
    }
}

struct SomeStructure {
    @SmallNumber var someNumber: Int
}

var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber) // wrapper 의 projected value 에 접근
// Prints "false"

someStructure.someNumber = 55
print(someStructure.$someNumber)
// Prints "true"
```

## Global and Local Variables

* Global 변수 : 함수, 메서드, 클로저 밖에서 정의된 변수
* Local 변수 : 함수, 메서드, 클로저 내에서 정의된 변수

* Stored 변수 : 특정 타입의 값을 저장할 수 있는 저장소 제공, 값을 설정하고 받을 수 있음
* Computed 변수 : 값을 저장하기보다 계산함. Global, local scope 내에 선언 가능.

**Global 상수, 변수는 항상 lazy 하게 연산됨. 단 `lazy` modifier 로 마킹되지 않아도 됨. Local 상수, 변수는 절대로 lazy 하게 연산되지 않음.**

Property wrapper 는 global 변수 / 연산 프로퍼티에 적용할 수 없음.

## Type Properties

**인스턴스가 아닌 타입 자체에 속하는 프로퍼티.** 인스턴스의 개수와는 상관없이 오직 프로퍼티의 copy 하나만 존재함.

특정 타입의 모든 인스턴스에 범용적인 값 정의할 때 유용.

* Stored 타입 프로퍼티 : 변수 / 상수
* Computed 타입 프로퍼티 : 변수

**저장 인스턴스 프로퍼티와는 다르게 타입 프로퍼티에는 항상 기본값을 줘야 함. 그 이유는 타입 자체는 이니셜라이저가 없어서 초기화 때 저장 프로퍼티에 값을 할당하는 개념이 없기 때문. 저장 타입 프로퍼티는 처음으로 접근될 때 lazy 하게 초기화 됨. 여러 개의 쓰레드에서 접근되더라도 오직 한 번만 초기화되는 게 보장됨. `lazy` modifier 로 마킹될 필요 없음.**

### Type Property Syntax

`static` 키워드를 붙여 선언. 클래스의 연산 타입 프로퍼티에는 `class` 를 붙여서 subclass 에서 override 할 수 있게 함.

```swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 1
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 6
    }
}
class SomeClass {
    static var storedTypeProperty = "Some value." // override 불가
    static var computedTypeProperty: Int {
        return 27
    }
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```

### Querying and Setting Type Properties

Dot syntax 사용

```swift
print(SomeStructure.storedTypeProperty)
// Prints "Some value."
SomeStructure.storedTypeProperty = "Another value."
print(SomeStructure.storedTypeProperty)
// Prints "Another value."
print(SomeEnumeration.computedTypeProperty)
// Prints "6"
print(SomeClass.computedTypeProperty)
// Prints "27"
```

