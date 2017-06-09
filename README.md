# ECMAScript 6에 대해 알아보자 ([원문](http://git.io/es6features))

[![Flattr Button](http://api.flattr.com/button/flattr-badge-large.png "번역이 마음에 드시면 기부 부탁드립니다!")](https://flattr.com/submit/auto?user_id=roylory&url=https://github.com/roylory/es6features-ko&title=es6features-ko&language=&tags=&category=)

## 소개
ECMAScript 6의 공식적인 이름은 ECMAScript 2015이며, 현재 최신 버전의 ECMAScript이다. 흔히 줄여서 ES6라고 쓴다.
ES6는 ES5가 2009년에 표준화된 이후 처음 업데이트된 것인데 매우 많은 변화가 있었다.
ES6를 완벽히 구현하는 자바스크립트 엔진의 개발은 아직 진행 중이며 [이곳](http://kangax.github.io/es5-compat-table/es6/)에서 진행 상황을 확인할 수 있다.

ECMAScript 6의 공식적인 전체 사양은 [이곳](http://www.ecma-international.org/ecma-262/6.0/)에서 확인할 수 있다.

ES6의 새로운 기능:
- [화살표 함수](#화살표-함수)
- [클래스](#클래스)
- [객체 리터럴의 진화](#객체-리터럴의-진화)
- [템플릿 문자열](#템플릿-문자열)
- [디스트럭쳐링](#디스트럭쳐링)
- [매개변수](#매개변수)
- [블록 영역 변수](#블록-영역-변수)
- [반복자](#반복자)
- [제너레이터](#제너레이터)
- [유니코드](#유니코드)
- [모듈](#모듈)
- [모듈 로더](#모듈-로더)
- [자료 구조 객체](#자료-구조-객체)
- [프락시](#프락시)
- [심볼](#심볼)
- [확장 가능한 내장 객체](#확장-가능한-내장-객체)
- [약속 객체](#약속-객체)
- [새로 추가된 API](#새로-추가된-api)
- [2진법과 8진법 표기](#2진법과-8진법-표기)
- [리플렉트 API](#리플렉트-api)
- [테일 콜](#테일-콜)

## ECMAScript 6의 새로운 기능

### 화살표 함수
화살표(arrow) 함수는 `=>`를 쓰는 표현식을 말하는데 `function`의 줄임 표현이다.
C#이나 Java 8, CoffeeScript 등에서도 비슷하게 쓰인다.
여러 구문을 넣으려면 `=>` 다음에 `{}`를 쓰면 된다.
화살표 함수는 일반 `function`과는 달리 새로운 클로져(closure)를 형성하지 않아서 `this`의 의미가 더 직관적이다.

```JavaScript
// 화살표 함수 표현식
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// 구문 포함
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// 화살표 함수 안에서 this 사용에 주목
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

더 많은 정보: [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### 클래스
ES6 클래스는 프로토타입 기반의 OO 패턴과 큰 차이가 없다.
다만 클래스를 이용하여 단 한 번만 선언함으로써 사용하기 편하고 상호 운용에 유리하다.
ES6의 클래스는 프로토타입 기반의 상속, 부모(super) 클래스 호출, 메서드, 정적 메서드, 생성자 등을 지원한다.

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

더 많은 정보: [MDN Classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

### 객체 리터럴의 진화
객체 리터럴은 다음과 같은 기능들이 추가됐다.
- 객체 생성 시 프로토타입 설정 가능
- `foo: foo`의 줄임 표현 가능
- 메서드 선언
- 부모(super) 호출
- 프로퍼티 이름을 동적으로 생성하기

이런 기능들 덕분에 객체 리터럴은 클래스 선언과 비슷하다. 그래서 객체 기반으로 프로그램 설계를 해도 클래스 기반 설계의 장점을 가질 수 있다.

```JavaScript
var obj = {
    // __proto__
    __proto__: theProtoObj,
    // ‘handler: handler’의 줄임 표현
    handler,
    // 메서드
    toString() {
     // 부모(super) 호출
     return "d " + super.toString();
    },
    // 동적으로 생성되는 프로퍼티 이름
    [ 'prop_' + (() => 42)() ]: 42
};
```

더 많은 정보: [MDN Grammar and types: Object literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Object_literals)

### 템플릿 문자열
템플릿 문자열(template string)은 문자열을 쉽게 다루도록 도와주는 새로운 문법이다.
Perl이나 Python 등의 언어에서 문자열 내삽(string interpolation) 기능과 비슷하다.
또한, 태그를 이용해서 맞춤형으로 문자열을 생성할 수도 있다. 태그는 인젝션 공격을 피하고 고차원적인 데이터 구조를 생성하게 도와준다.

```JavaScript
// 기본 예제
`In JavaScript '\n' is a line-feed.`

// 다중 라인 컴멘트
`In JavaScript this is
 not legal.`

// 문자열 내삽(string interpolation)
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// HTTP request의 prefix를 생성하기
POST`http://foo.org/bar?a=${a}&b=${b}
     Content-Type: application/json
     X-Credentials: ${credentials}
     { "foo": ${foo},
       "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

더 많은 정보: [MDN Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings)

### 디스트럭쳐링
디스트럭쳐링(destructuring)은 여러 변수에 값을 할당할 때 배열이나 객체의 패턴으로 손쉽게 바인딩해주는 것을 말한다.
만약 패턴에 매칭하는 값이 없으면 `undefined`가 할당된다. 객체 리터럴에서 `foo["bar"]`가 `undefined`가 되는 것과 마찬가지다.

```JavaScript
// 배열 매칭
var [a, , b] = [1,2,3];

// 객체 매칭
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// 더 간단한 객체 매칭 방법
var {op, lhs, rhs} = getASTNode()

// 매개변수를 넘길때도 이용된다
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// 값이 없을 경우
var [a] = [];
a === undefined;

// 기본값 설정 가능
var [a = 1] = [];
a === 1;
```

더 많은 정보: [MDN Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

### 매개변수
ES6에서는 매개변수와 관련하여 다음과 같은 새로운 기능들이 추가됐다.
- 호출되는 함수는 매개변수의 기본값을 가질 수 있다.
- `...`을 쓰면 배열의 각 값을 손쉽게 인자로 넘길 수 있다.
- 나머지 매개변수들을 배열로 받아올 수 있다. 더 이상 `arguments`를 쓸 필요가 없어졌다.

```JavaScript
function f(x, y=12) {
  // 매개변수 y가 지정되지 않으면 y는 12가 된다 (아니면 undefined가 넘어오거나)
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y는 배열이다
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// 배열의 각 값을 인자로 넘길 수 있다
f(...[1,2,3]) == 6
```

더 많은 정보: [Default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters), [Rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters), [Spread Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

### 블록 영역 변수
`let`과 `const`는 블록 영역 변수를 선언하는 새로운 키워드다.
`let`은 새로운 `var`라고 생각하면 되고,
`const`는 상수형 변수다.
상수형 변수는 반드시 값이 할당되어야 하고 다시 못 바꾼다.

```JavaScript
function f() {
  {
    let x;
    {
      // 블록 영역
      const x = "sneaky";
      // 에러 - 상수형 변수는 못바꾼다
      x = "foo";
    }
    // 에러 - 이미 let으로 선언되었음
    let x = "inner";
  }
}
```

더 많은 정보: [let statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

### 반복자
반복자(iterator) 객체는 .NET의 IEnumerable이나 Java의 Iterable과 비슷하다.
`for..in` 대신 `for..of`를 쓰면 반복자로 반복할 수 있다.
LINQ처럼 lazy design pattern이기 때문에 따로 배열을 만들지 않는다.

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // 시퀀스를 1000에서 끊는다
  if (n > 1000)
    break;
  console.log(n);
}
```

다음과 같은 덕-타입형 인터페이스로 반복자를 이해할 수 있다. (이해를 쉽게하기 위해 [TypeScript](http://typescriptlang.org)를 썼음):

```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

더 많은 정보: [MDN for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)

### 제너레이터
제너레이터(generator)는 `function*`과 `yield`를 사용해서 반복자 사용을 간편하게 한다.
`function*`로 선언된 함수는 제너레이터 인스턴스를 반환한다.
제너레이터는 반복자에서 `next`와 `throw`가 추가된 특수한 반복자 타입이라고 볼 수 있다.
즉, `yield` 명령어를 써서 특정 값을 반환하고 (혹은 예외 처리하고) 그 값을 다시 제너레이터에 입력해주는 것이다.

참고: ‘await’ 같은 비동기 프로그래밍을 위해 쓰일 수도 있다. ES7의 `await` 제안서를 참고.

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

제너레이터 인터페이스 (이해를 쉽게하기 위해 [TypeScript](http://typescriptlang.org)를 썼음):

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

더 많은 정보: [MDN Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

### 유니코드
유니코드와 관련하여 다음과 같은 새로운 기능들이 추가됐다.
- 모든 유니코드 지원
- 정규 표현식(RegExp)에서 `u` 모드
- 문자열에서 `\u`로 유니코드 표현 가능
- 이제 codePointAt() 등의 메서드를 이용해 21-bit 코드 포인트 유니코드를 제대로 처리할 수 있다

이러한 새로운 기능들은 자바스크립트로 글로벌한 앱을 만드는 데 도움이 될 것이다.

```JavaScript
// ES5.1와 같음
"𠮷".length == 2

// RegExp에서 ‘u’를 쓸 수 있다
"𠮷".match(/./u)[0].length == 2

// 문자열에서 유니코드 쓰기
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// 유니코드 관련 스트링 메서드
"𠮷".codePointAt(0) == 0x20BB7

// for-of 구문으로 code point 단위로 끊기
for(var c of "𠮷") {
  console.log(c);
}
```

더 많은 정보: [MDN RegExp.prototype.unicode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)

### 모듈
ES6는 프로그래밍 언어 차원에서 컴포넌트를 정의하는 모듈을 지원한다.
이것은 AMD나 CommonJS 등 유명한 자바스크립트 모듈 로더들에서 쓰이는 패턴을 따온 것이다.
런타임 동작은 기본 로더에서 정의할 수 있다.
또한, 모듈은 묵시적으로 비동기적이다 – 즉, 실제로 호출되기 전까진 실행되지 않는다.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

`export default`와 `export *` 사용 예제:

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
```
```JavaScript
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
```

더 많은 정보: [import statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), [export statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

### 모듈 로더
모듈 로더(module loader)는 다음을 지원한다:
- 동적 로딩
- 상태 격리(state isolation)
- 전역 네임스페이스 격리(global namespace isolation)
- 컴파일 후크(compilation hooks)
- 중첩 가상화(nested virtualization)

기본 모듈 로더를 바꿀 수 있다. 또한, 새로 만든 로더는 격리되거나 제한된 맥락에서 사용될 수 있다.

```JavaScript
// 동적 로딩 – ‘System’은 기본 로더이다
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// new Loaders 안에 실행 샌드박스를 만듬
var loader = new Loader({
  global: fixup(window) // ‘console.log’를 대치
});
loader.eval("console.log('hello world!');");

// 모듈 캐쉬를 직접 조작
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
```

### 자료 구조 객체
ES6에서는 `Set`, `Map`, `WeakMap`, `WeakSet` 등 일반적인 알고리즘에 많이 쓰이는 자료 구조 객체가 추가되었다.
`WeakMap`은 객체를 키(key)로 하는 참조 테이블을 만들 때 발생하는 메모리 누수 현상을 잡아준다.

```JavaScript
// Set
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Map
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// WeakMap
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// WeakSet
var ws = new WeakSet();
ws.add({ data: 42 });
// 추가된 객체가 어디에서도 참조되지 않기 때문에 WeakSet에 추가되지 않는다
```

더 많은 정보: [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap), [WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

### 프락시
프락시(proxy)는 임의의 객체에 가능한 모든 기능을 가로채서 작동하는 다른 객체를 생성해준다.
프락시는 인터셉션(interception), 객체 가상화, 로깅/프로파일링 등에 유용하다.

```JavaScript
// 일반 객체의 프락시
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// 함수 객체의 프락시
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

프락시 트랩(프로퍼티 접근 메서드) 목록:

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

더 많은 정보: [MDN Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

### 심볼
심볼은 객체 상태(object state)의 접근 제어(access control)를 가능하게 해준다.
ES6에서는 객체 리터럴에서 `symbol`을 키(key)로 사용할 수 있다. 물론 `string`을 써도 된다.
심볼은 새로 추가된 원시(primitive) 타입이다. `description` 매개변수를 이용하면 디버깅할 때 편리할 수 있다 - 하지만 필수는 아니다.
심볼은 유일무이하다 (gensym 함수처럼), 그러나 `Object.getOwnPropertySymbols` 등의 리플렉션(reflection) 기능으로 노출될 수 있으므로 비공개적이지는 않다.

```JavaScript
var MyClass = (function() {

  // 모듈 영역의 심볼
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
```

더 많은 정보: [MDN Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

### 확장 가능한 내장 객체
ES6에서는 `Array`, `Date`, 그리고 DOM `Element` 같은 내장 객체가 서브클래스로 확장 가능하다.

객체를 생성할 때 사용하는 `Ctor` 함수는 이제 다음의 2단계를 가진다 (둘 다 가상적으로 불려서 실행된다):
- `Ctor[@@create]`를 호출하여 특별한 동작을 하게끔 객체를 할당한다
- 새 인스턴스의 생성자를 호출하여 초기화한다

`@@create` 심볼은 `Symbol.create`을 통해서 가능한 것이다.
내장 객체는 `@@create`를 명시적으로 노출한다.

```JavaScript
// Array(배열)의 수도코드
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // 'length'를 업데이트하기 위해
        // 특별한 [[DefineOwnProperty]]를 설치한다
    }
}

// Array(배열)의 서브클래스
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// 'new'의 2단계:
// 1) 객체를 할당할 때 @@create를 호출한다
// 2) 새 인스턴스의 생성자를 호출한다
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### 새로 추가된 API

수많은 API가 기존의 라이브러리에 추가되었다. 예를 들자면, 핵심 Math 메서드, 배열 변환 도움 메서드, 문자열 메서드, `Object.assign` 등이 있다.

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // NodeList를 배열로 변환
Array.of(1, 2, 3) // 인자가 하나일 때만 제외하고 new Array(...)와 비슷하다.
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // 반복자 [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // 반복자 0, 1, 2
["a", "b", "c"].values() // 반복자 "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

더 많은 정보: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math), [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from), [Array.of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/of), [Array.prototype.copyWithin](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin), [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

### 2진법과 8진법 표기
ES6에서는 정수를 `b`(2진법)이나 `o`(8진법)으로 표기할 수 있다.

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### 약속 객체
약속(promise) 객체는 비동기 프로그래밍을 위한 라이브러리다.
이것은 미래에 사용 가능할 값을 세련되게 표현해준다.
약속 객체는 이미 기존의 많은 자바스크립트 라이브러리에서 사용되고 있다.

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

더 많은 정보: [MDN Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

### 리플렉트 API
리플렉트(reflect) API를 이용하면 런타임 환경에서 객체의 모든 meta-operation을 행할 수 있다.
리플렉트 API는 프락시 트랩과 정확히 일치하는 정적 메서드 목록을 지원한다.
그래서 프락시를 구현하는데 매우 유용하다.

```JavaScript
// No sample yet
```

더 많은 정보: [MDN Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

### 테일 콜
테일 콜(tail call), 즉 `return` 구문에서 호출되는 함수는 더 이상 스택(stack)에 쌓이지 않는다.
이제 재귀적인 알고리즘을 걱정 없이 쓸 수 있다.

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// 현재 대부분의 자바스크립트 엔진에서는 stack overflow 에러가 날테지만,
// ES6에서는 input이 커도 문제 없다.
factorial(100000)
```
