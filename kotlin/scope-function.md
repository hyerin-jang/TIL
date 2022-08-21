# Scope Function
- scope: 영역, function: 함수
- scope function: 일시적인 영역을 형성하는 함수
- 람다를 사용해 일시적인 영역을 만들고 코드를 더 간결하게 만들거나 method chaining에 활용하는 함수

## Scope Function 분류
|         | it 사용 | this 사용 |
|---------|-------|---------|
| 람다의 결과  | let   | run     |
| 객체 그 자체 | also  | apply   |
|         | with  |         |

- this: 생략이 가능한 대신 다른 이름을 붙일 수 없음
- it: 생략이 불가능한 대신 다른 이름을 붙일 수 있음

## let
````
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
    // return block()
}
````
- let은 일반 함수를 파라미터로 받아 함수 내부에서 호출
- 사용 목적
  - 하나 이상의 함수를 call chain 결과로 호출할 때 사용
    ````
    val strings = listOf("apple", "car")
    strings.map { it.length }
        .filter { it > 3 }
        .let(::println)
        //.let { length -> println(length) }
    ````
  - non-null 값에 대해서만 code block을 실행시킬 때
    ````
    val length = str?.let {
        println(it.uppercase())
        it.length
    }
    ````
  - 일회성으로 제한된 영역에 지역 변수를 만들 때
    ````
    val numbers = listOf("one", "two", "three", "four")
    val modifiedFirstItem = numbers.first()
        .let { firstItem ->
            if (firstItem.length >= 5) firstItem else "!$firstItem!"
        }.uppercase()
    println(modifiedFirstItem)
    ````
    
## run
````
public inline fun <T, R> T.run(block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block()
}
````
- 사용 목적
  - 객체 초기화와 반환 값의 계산을 동시에 해야할 때 사용
  - 객체를 만들어 DB에 바로 저장하고 그 인스턴스를 활용할 때
    ````
    val person = Person("이름", 100).run(personRepository::save)
    ````    
    ````
    val person = Person("이름", 100).run {
        hobby = "독서"
    personRepository.save(this)
    }
    ````
- 반복되는 생성 후처리는 생성자, 프로퍼티, init block으로 넣는 것이 좋음

## apply
````
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
````
- apply는 객체 그 자체가 반환됨
- 사용 목적
  - 객체 설정을 할 때에 객체를 수정하는 로직이 call chain 중간에 필요할 때
  - test fixture를 만들 때
    ````
    fun createPerson(
        name: String,
        age: Int,
        hobby: String,
    ): Person {
        return Person(
            name = name,
            age = age,
        ).apply {
            this.hobby = hobby
        }
    }
    ````
    
## also
````
public inline fun <T> T.also(block: (T) -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block(this)
    return this
}
````
- also는 객체 그 자체가 반환됨
- 사용 목적
  - 객체를 수정하는 로직이 call chain 중간에 필요할 때
    ````
    mutableListOf("one", "two", "three")
        .also { println("four 추가 이전 지금 값: $it") }
        .add("four")
    ````

## with
- 사용 목적
  - 특정 객체를 다른 객체로 변환해야 하는데 모듈 간의 의존성에 의해 정적 팩토리 혹은 toClass 함수를 만들기 어려울 때
    ````
    return with(person) {
        PersonDto(
        name = name,
        age = age,
        )
    }
    ````
    - this를 생략할 수 있어 필드가 많아도 코드가 간결해짐