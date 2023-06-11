# NomadCoder TypeScript 강의

## ts 장점

- 런타임 오류 줄일 수 있음.

## ts의 타입들

```ts
📌 Types of TS(part I) ----

✅ 배열: 자료형[]
✅ 숫자: number
✅ 문자열: string
✅ 논리: boolean
✅ optional
const player : {
    name: string,
    age?:number
} = {
    name: "Gemma"
}
❌ player.age가 undefined일 가능성 알림
if(player.age < 10) {
}
⭕ player.age가 undefined일 가능성 체크
if(player.age && player.age < 10) {
}
❗ ?를 :앞에 붙이면 optional


✅ Alias(별칭) 타입
Type Aliases : 객체 타입뿐만 아니라 모든 타입에 이름을 지정 가능.
type Player = {
    name: string, //required
    age?: number //? -> ::optional type::
}

const gemma: Player = {
    name: "Gemma"
}

const amy: Player ={
    name: "Amy",
    age: 28
} //이런식으로 재사용 가능

//---
type Name = string;
type Age = number;

type Player = {
    name: Name,
    age?: Age  //이런식으로 타입을 설정하는 것도 가능함.
                //단, 너무 과한 재사용은 자제할 것.
}

⭐  function 선언문
function makePlayer(name: string) : Player ={
	return {
		name // name: name
	}
}
❗함수가 리턴할 타입 알려준다.  : Player

⭐  arrow function
const makePlayerArrow = (name: string) : Player => ({name});
❗name: string -> argument의 타입 설정
❗:Player -> return 값 타입 설정

const gemma = makePlayer("Gemma");
gemma.age = 29; // : Player 해줬기 때문에 가능.



📌 Types of TS(part II) ----

✅ readonly 사용하기
JavaScript에서는 mutability(변경 가능성)이 기본값이지만 타입스크립트에서는 readonly를 통해 읽기 전용으로 만들 수 있음.
type Player = {
    readonly name:string
    age?:number
}

const playerMaker = (name: string): Player => ({name})

const gemma = playerMaker("Gemma")
🚫 gemma.name = "aa"

const numbers: readonly number[] = [1, 2, 3, 4]
🚫 numbers.push(1)
❗ readonly가 있으면 최초 선언 후 수정 불가
    ⇒ immutability(불변성) 부여
        but, javascript에서는 그냥 배열

✅ Tuple
정해진 개수와 순서에 따라 배열 선언
const player: [string, number, boolean] = ["gemma", 1, true]
❗ readonly도 사용가능 ⇒ readonly [...] 형태
const player: readonly [string, number, boolean] = ["gemma", 1, true];

✅ any, undefined, null
any: 아무 타입 -> ts 기능 비활성화
undefined: 선언X 할당X
null: 선언O 할당X



📌 Types of TS(part III) ----
✅ unknown
- unknown타입은 모든 값 나타냄.
- any타입과 비슷하지만 any보다 unknown이 더 안전 -> 이유는 unknown값으로 작업을 수행하는 것은 합법적이지 않기 때문.
let a:unknown

if(typeof a === 'number'){
    let b = a + 1
}
if(typeof a === 'string'){
    let b = a.toUpperCase()
}
🚫 let b = a + 1

✅ void
- void는 값을 반환하지 않는 함수의 반환 값 나타낼때.
- 함수에 return 문이 없거나 해당 return 문에서 명시적 값을 반환하지 않을 때 항상 유추되는 타입.
function hello() {
    console.log('x')
}
const a = hello()
🚫 a.toUpperCase()

✅ never
- 함수가 return하지 않을 때.
- 함수가 예외를 throw하거나 프로그램 실행을 종료함을 의미.
function hello():never {
    throw new Error("zzz")
    🚫return "a"
}

function temp(name:string|number):never {
    if(typeof name === "string"){
        name
    } else if(typeof name === "number"){
        name
    } else {
        name
    }
}
if 안에서는 string형의 name 반환
else if 안에서는 number형의 name 반환
else 안에서는 never형의 name 반환
⇒ 즉, 제대로 인자가 전달되었다면 else로 올 수 없음

```

## ts에서 함수 사용

- call signature
- overloading
- polymorphism (다형성)
- generics

✅ call signature

- 매개변수와 반환 타입을 지정
- 마우스 올리면 타입 보이는...

```ts
❗️타입을 먼저 생각하고 -> 코드를 작성할 수 있게 해줌.
type Add = {a:number, b: number} => number;

const add:Add = (a,b) => a+b

```

\
✅ overloading [funciton(=method) overloading]

- 외부라이브러리나 패키지 사용시 많이 사용
- 하나의 함수가 여러개의 call signature 가질 때 사용

```ts

❗️매개변수의 데이터 타입이 다른 경우 예외 처리
type Add = {
(a: number, b: number): number,
(a: number, b: string): number
}

const add: Add = (a, b) => {
if (typeof b === "string") return a;
return a + b;
}


❗️ 매개변수의 개수가 다른 경우 예외 처리
type Add2 = {
(a: number, b: number): number,
(a: number, b: number, c: number): number
}

const add2: Add2 = (a, b, c?: number) => {
if (c) return a + b + c;
return a + b;
}

```

✅ polymorphism(다형성)

- ❓poly란?
  - many, serveral, much, multi 등과 같은 뜻
- ❓morphos란?
  - form, structure 등과 같은 뜻
- ❗polymorphos = poly + morphos = 여러 다른 구조

```ts
🔆 concrete type
- number, boolean, void 등 지금까지 배운 타입

🔆generic type
- 타입의 placeholder(something that will be replaced.)같은 것.

─ 예시 1
type superPrint = <T>(arr: T[]) => T
// T는 무슨 이름이든 괜찮음
type superPrint = {
    <TypePlaceholder> (arr:TypePlaceholder[]) :TypePlaceholder
}


const superPrint:SuperPrint=(arr)=>{
return arr[0]
}
const a=superPrint([1,2,3])
const b=superPrint([true,false,true])
const c=superPrint(["a","b"])
const d=superPrint([1,2,"a","b",true])
❗️ 각 함수에 맞는 call signature 자동으로 ts가 만들어줌.
❗️any를 쓰면 ts의 보호를 받을 수 없음.

🔆 any vs generics
* 'any'를 사용하는 것은 어떤 타입이든 받을 수 있다는 점에서 'generics'과 같지만 함수를 반환하는데 있어 'any'는 받았던 인수들의 타입을 활용하지 못한다

* 즉, 'generics'은 어떤 타입이든 받을 수 있다는 점에서 'any'와 같지만 해당 정보를 잃지 않고 타입에 대한 정보를 다른 쪽으로 전달할 수 있다는 점이 다르다

-예시 2
❗️ 여러개의 generic을 사용할 수 있음.
type superPrint = <T,M>(a: T[] b: M) => T
const superPrint:SuperPrint=(arr)=>{
return arr[0]
}
const a=superPrint([1,2,3],"x")
const b=superPrint([true,false,true],2)

---
❗️대부분은 위와 같이 generic 직접 만들어 사용하는 경우는 거의 없고, 라이브러리나 외부 패키지 사용시 자주 사용

---
type superPrint = <T>(a: T[]) => T
const superPrint:SuperPrint=(arr)=>{
return arr[0]
}
두개 합쳐서 아래와 같이 사용 많이 함.
function superPrint<T>(a:T[]){
    return a[0];
}

---
⭐️⭐️⭐️
'generic'은 선언 시점이 아니라 '생성 시점'에 타입을 명시하여 하나의 타입만이 아닌 '다양한 타입을 사용'할 수 있도록 하는 기법이다.

/*
자바스크립트처럼 여러 타입을 받을수 있다. 하지만 자바스크립트는 any타입이 설정되어 해당 변수가 number타입인 경우에 touppercase같이 스트링에만 사용되는 method를 사용할 경우 컴파일 과정에서 문제없이 진행되고 런타임 과정에서 에러가 뜨는 상황이 발생한다.
하지만 타입스크립트에서 제네릭을 사용하게되면 일반 자바스크립트처럼 자동 타입 할당이 되고 소스 작성 과정 중에 ts이 해당 변수가 number타입인걸 알게되고 touppercase같이 스트링 전용 method 사용이 불가능하다. 에러가 뜨게되서 런타임 과정중에 에러가 나지 않도록 막아준다.
*/

- 예시 3
type Player = {
name: string,
extraInfo: T
};

type MePlayer = Player;
//type MePlayer = Player<{age:number}>;

type MeExtra = {age: number};

const player: MePlayer = {
//const player: Player<{age:number}> ={}
name: "gemma",
extraInfo: {
age: 29
}
};

const player2: Player = {
name: "amy",
extraInfo: null
};

```

## ts의 Classes & Interfaces

✅ Class

- 추상(abstract) 클래스
  - 다른 클래스가 상속받을 수 있는 클래스
  - 직접 새로운 인스턴스를 만들 수는 없다.

```ts
abstract class User{
constructor(
private firstname:string,
private lastname:string,
public nickname:string
){
abstract getNickname():void
}
}

class Player extends User{
// 추상 메서드는 추상 클래스를 상속받는 클래스들이 반드시 구현(implement)해야하는 메서드이다.
getNickname(){
console.log(this.nickname)
}
}
```

📌접근 가능한 위치
구분 |　선언한 클래스 내 |상속받은 클래스 내 |　인스턴스
|---|:---:|:---:|:---:|
private| ⭕ | ❌ | ❌
protected| ⭕ | ⭕ | ❌
public| ⭕ | ⭕ | ⭕

- private: 해당 클래스 내에서만 접근 가능 (자식 클래스에서도 접근 불가)
- protected: 해당 클래스와 자식 클래스에서 접근 가능
- public: 모든 클래스에서 접근 가능

🔆 TIP)
private를 사용하면 상속받은 클래스 안에서 마저도 this 사용해 접근 불가능 \
그래서 protected를 사용하면 상속받은 클래스 안에서 this 사용해 접근 가능 \
물론 protected로 지정된 것들은 외부에서 사용이 불가능\
추상클래스 안에 메소드는 적어서는 안되고 call signature만 적어야 함\
추상클래스 안의 메소드는 결국 구현이 되지 않는다고 나옴
