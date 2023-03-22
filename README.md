# 템플릿 문법

## 보간법

### 문자열
```html
<span>메세지: {{ msg }}</span>
```
```plaintext
데이터 바인딩의 가장 기본 형태는 이중괄호구문 기법을 사용한 문자열 보간법
```

```html
<span v-once>결코 변하지 않는다: {{ msg }}</span>
```
```plaintext
v-once 디렉티브를 사용하면 데이터가 변경되어도 갱신되지 않는 일회성 보간을 수행할 수 있다
즉 반응성을 가지지 않는다.
```

### 원시 HTML
```html
<p>이중 괄호 사용: {{ rawHtml }}</p>
<p>v-html 디렉티브 사용: <span v-html="rawHtml"></span></p>
```
```plaintext
이중 중괄호는 데이터를 HTML이 아닌 일반 텍스트로 해석한다. 실제 HTML을 출력하려면
v-html 디렉티브를 사용해야한다.
```

## 속성
```html
<div v-bind:id="dynamicId"></div>
```
```plaintext
이중 중괄호 구문은 HTML 속성에 사용할 수 없다. 대신 v-bind 디렉티브를 사용한다.
```

## Computed

```html
<template>
  <section v-if="hasFruit">
    <h1>Fruits</h1>
    <ul>
      <li
        v-for="fruit in fruits"
        :key="fruit">
        {{ fruit }}
      </li>
    </ul>
  </section>
  <section>
    <h1>Reverse Fruits</h1>
    <ul>
      <li
        v-for="fruit in reverseFruits"
        :key="fruit">
        {{ fruit }}
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  data() {
    return{
      fruits:[
        'Apple','Banana','Cherry'
      ]
    }
  },
  computed: {
    hasFruit() {
      return this.fruits.length >0
    },
    reverseFruits() {
    return this.fruits.map(fruit => {
      // 'Apple' => ['A','p','p','l','e']
      // ['e','l','p','p','A'] => 'elppA'
      return fruit.split('').reverse().join('')
    })
  }
  }
}
</script>
```
```plaintext
computed 는 즉 계산된 데이터이다. data 옵션에 정의 해놓은 특정한 데이터를 추가적으로
연산을 통해서 정의를 한다음에 그 정의된 값을 반환해서 사용할 수 있는 새로운 데이터 이다.
```

## Computed 캐싱
```html
<template>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reverseMessage() }}</h1>
  <h1>{{ reverseMessage() }}</h1>
  <h1>{{ reverseMessage() }}</h1>
  <h1>{{ reverseMessage() }}</h1>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Hello Computed!'
    }
  },
  computed: {
    reversedMessage() {
      return this.msg.split('').reverse().join('')
    }
  },
  methods: {
    reverseMessage() {
      return this.msg.split('').reverse().join('')
    }
  }
}
</script>
```
```plaintext
methode 안에 있는 데이터는 반복적으로 화면에 춟력할 때 출력 할때마다 계산하여 출력한다. 하지만
computed 안에 있는 계산된 데이터는 캐싱이라는 기능이 있기 때문에 반복적으로 데이터를 
화면에 출력할 때 다시 한번 계산하지 않는다. 한번 계산된 데이터를 계속해서 출력한다.
```

## Getter, Setter
```html
<template>
  <button @click="add">
    ADD
  </button>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
</template>

<script>
import { get } from 'http'

export default {
  data() {
    return {
      //Getter, Setter 둘다 가능하다.
      msg: 'Hello Computed!'
    }
  },
  computed: {
    //Getter
     reversedMessage() {
       return this.msg.split('').reverse().join('')
     }
  },
  methods: {
    add() {
      // Getter 에 할당을 해봤자 값 변동이 안된다.
      this.reversedMessage += '!?'
      // Setter 기능도 있는 부분에 할당을 해야지 값이 변경이 된다.
      this.msg += '!!!?'
    }
  }
}
</script>
```
```plaintext
reversedMessage() 는 읽기 전용이다. 즉 할당연산자를 사용해서 어떤 값을 할당을 하더라도
그것이 동작하는 구조는 아니다. 그래서 값을 얻어내는 용도로만 사용을 한다.
하지만 Vue.js 문법을 통해서 Getter,Setter 이 둘다 사용 가능하게 만들 수 있다.
```
```html
<template>
  <button @click="add">
    ADD
  </button>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
</template>

<script>
export default {
  data() {
    return {
      //Getter, Setter 둘다 가능하다.
      msg: 'Hello Computed!'
    }
  },
  computed: {
    reversedMessage: {
      get() {
        return this.msg.split('').reverse().join('')
      },
      set(newValue) {
        this.msg = newValue
      }
    }
  },
  methods: {
    add() {
      // 값 변경가능
      this.reversedMessage += '!?'
      
  }
}
</script>
```
```plaintext
위와 같이 getter과 setter를 사용해주면 값 할당이 가능해진다.
1. add() 가 실행이 되면 set()매소드가 실행이 되고
2. set의 value부분에는 값이 할당된 데이터가 들어가게 된다. (this.reversedMessage += '!?')
3. 그 데이터가 this.msg = value 를 통해 msg에 할당이 되면서 
4. get()에서 변형된 값을 가지고 계산하여 출력한다.
```

## Watch
```html
<template>
  <h1 @click="changeMessage">
    {{ msg }}
  </h1>
  <h1>{{ reversedMessage }}</h1>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Hello?'
    }
  },
  computed: {
    reversedMessage() {
      return this.msg.split('').reverse().join('')
    }
  },
  watch: {
    msg() {
      console.log('msg: ',this.msg)
      console.log('reversedMessage: ',this.reversedMessage)
    }
  },
  methods: {
    changeMessage() {
      this.msg = 'Good!'
    }
  }
}
</script>
```
```plaintext
1. changeMessage() 의 메소드를 통해 해당하는 msg라는 데이터를 할당연산자로 변경을 하고있다.
2. watch 라는 옵션을 통해 지정한 msg라는 데이터는 이제 감시되고 있는 상태이다.
3. 감시가 되고 있다가 실제로 변경이 일어나니까 msg()메소드를 실행하게 된다.
계산된 값 또한 감시가 가능하다.
```

## 클래스와 스타일 바인딩
### 클래스 바인딩
```html
<template>
  <h1
    :class="{active: isActive}"
    @click="activate">
    Hello?!({{ isActive }})
  </h1>
</template>

<script>
export default {
  data() {
    return {
      isActive: false
    }
  },
  methods: {
    activate() {
      this.isActive = true
    }
  }
}
</script>

<styl scoped>
  .active {
    color: red;
    font-size: 60px;
    font-weight: bold;
  }
</style>
```
```plaintext
h1태그의 클래스로 activ라는 이름의 class를 추가 하려는데, isActive가 false면 추가가 안되고
true면 추가가 된다. 이런 표기법을 클래스 바인딩이라고 한다.
```
### 스타일 바인딩
```html
<template>
  <h1
    :style="{color: color, fontSize: fontSize}"
    @click="changestyle">
    Hello?!
  </h1>
</template>

<script>
export default {
  data() {
    return {
      color: 'orange',
      fontSize: '30px'
    }
  },
  methods: {
    changestyle() {
      this.color = 'red',
      this.fontSize = '50px'
    }
  }
}
</script>
```
```plaintext
위 와 같이 style를 데이터처럼 활용을 해서 스타일 바인딩을 할 수 있다.
```
```html
<template>
  <h1
    :style="[fontStyle, backgroundColor]"
    @click="changestyle">
    Hello?!
  </h1>
</template>

<script>
export default {
  data() {
    return {
      fontStyle: {
      color: 'orange',
      fontSize: '30px'
      },
      backgroundColor: {
        backgroundColor: 'black'
      }
    }
  },
  methods: {
    changestyle() {
      this.fontStyle.color = 'red',
      this.fontStyle.fontSize = '50px'
    }
  }
}
</script>
```
```plaintext
적용할 스타일이 여러개라면 위 와 같이 객체로 묶어서 사용도 가능하다.
여러개의 객체 데이터를 연결할 떄는 배열데이터로 연결해서 사용한다.
```

## 조건부 렌더링
### v-if
```html
<template>
  <button @click="handler">
    Click me!
  </button>
  <h1 v-if="isShow">
    Hello?!
  </h1>
  <h1 v-else-if="count > 3">
    3번 이상 클릭 했습니다.
  </h1>
  <h1 v-else>
    Good~
  </h1>
</template>

<script>
export default {
  data() {
    return {
      isShow: true,
      count: 0
    }
  },
  methods: {
    handler() {
      this.isShow = !this.isShow
      this.count += 1
    }
  }
}
</script>
```
```plaintext
흔히 알고 있는 if 문이라고 생각하면 된다. 
v-if="isShow" 의 isShow 값이 true일 때 화면에 출력이 되고 v-else는 출력이 안된다.
v-if="isShow" 의 isShow 값이 false일 때 회면에 출력이 안 되고 v-else는 출력이 된다.
버튼의 핸들러를 추가시켜 값을 변환 시키게 할 수 있다.
```

### v-show
```html
<template>
  <button @click="handler">
    Click me!
  </button>
  <h1 v-show="isShow">
    Hello?!
  </h1>
</template>

<script>
export default {
  data() {
    return {
      isShow: false,
      count: 0
    }
  },
  methods: {
    handler() {
      this.isShow = !this.isShow
      this.count += 1
    }
  }
}
</script>
```
```plaintext
v-if와 비슷하다. 차이점은 v-show가 있는 엘리먼트는 항상 렌더링 되고 DOM에 남아있다는 점입니다. 
v-show는 단순히 엘리먼트에 display CSS 속성을 토글합니다.
무언가를 자주 전환 해야 한다면 v-show를 사용하는게 좋고 런타임 시 조건이 변경되지 않는다면 v-if를 사용하는 게 더 낫습니다.
```

## 리스트 렌더링
### v-for
```html
<template>
  <ul>
    <li
      v-for="fruit in fruits"
      :key="fruit">
      {{ fruit }}
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      fruits: ['Apple', 'Banana', 'Cherry']
    }
  }
}
</script>
```
```plaintext
fruits 의 배열 데이터를 fruit의 넣고 배열의 갯수만큼 반복해서 출력한다.
key값은 fruit의 데이터가 고유하다라는 뜻이다.
```

## 이벤트 핸들링
### v-on: click="methodName"
```html
<template>
  <button @click="handlerA(), handlerB()">
    Click me!
  </button>
</template>

<script> 
export default {
  methods: {
    handlerA() {
      console.log('A')
    },
    handlerB() {
      console.log('B')
    }
  }
}
</script>
```
```plaintext
말 그대로 클릭 이벤트 이다. 문법은 v-on: click="methodName" 을 @click="methodName" 로 줄여서 사용이 가능하다.
클릭시 사용할 메소드가 한개 이상이라면 소괄호를 열고 닫아서 작성을 해줘야한다. 
만약 한개라면 소괄호는 작성을 안해도 된다.
```

## 이벤트 핸들러 수식어

### prevent
```html
<template>
  <a
    href="https://naver.com"
    target="_blank"
    @click.prevent="handler">
    NAVER
  </a>
</template>

<script>
export default {
  methods: {
    handler() {
      console.log('ABC!!')
    }
  }
}
</script>
```
```plaintext
기본 기능을 작동하는걸 멈추게한다. A태그같은 경우 주소 이동이 기본 기능이지만,
prevent라는 수식어를 달아주면 기본기능을 막아준다.
```
### once
```html
<template>
  <a
    href="https://naver.com"
    target="_blank"
    @click.once="handler">
    NAVER
  </a>
</template>

<script>
export default {
  methods: {
    handler() {
      console.log('ABC!!')
    }
  }
}
</script>
```
```plaintext
지금 상황에서 링크를 누르면 여러면 작동을 하지만 methods는 단 한번만 작동을 한다.
once수식어는 특정이벤트가 발생했을 때 메소드를 단 한번만 실행하도록 해준다.
```

### 수식어체이닝
```html
<template>
  <a
    href="https://naver.com"
    target="_blank"
    @click.prevent.once="handler">
    NAVER
  </a>
</template>

<script>
export default {
  methods: {
    handler() {
      console.log('ABC!!')
    }
  }
}
</script>
```
```plaintext
수식어는 체이닝 방법으로 여러개를 붙혀서 사용도 가능하다.
위와 같이 수식어를 작성하게 되면 A의 기본 기능을 막는 prevent는 단 한번만 작동하게 된다.
그 후 다시 클릭시 링크이동이 된다.
```

### stop
```html
<template>
  <div
    class="parent"
    @click="handlerA">
    <div
      class="child"
      @click="handlerB"></div>
  <!--@click.stop="handlerB" -->
  </div>
</template>

<script>
export default {
  methods: {
    handlerA() {
      console.log('A')
    },
    handlerB() {
      console.log('B')
    }
  }
}
</script>
```
```plaintext
위 상황에서  .child를 클릭을 해도 이벤트 버블링으로 인해 .parent도 같이 클릭이 된다.
그래서 console를 보게 되면 B가 찍히고 그리고 A도 찍히는걸 볼 수 있다.
그걸 방지하기 위해서 이벤트핸들러 수식어로 stop를 작성하면 된다.
```

### capture (이벤트 캡처링)
```html
<template>
  <div
    class="parent"
    @click="handlerA">
<!--@click.capture="handlerA" -->
    <div
      class="child"
      @click="handlerB"></div>
  </div>
</template>

<script>
export default {
  methods: {
    handlerA() {
      console.log('A')
    },
    handlerB() {
      console.log('B')
    }
  }
}
</script>
```
```plaintext
위 상황에서 .child를 클릭하게 되면 console에서는 B가 찍히고 그 다음으로 A가 찍히게 된다.
.praent 부분에 capture수식어를 작성하게 되면
.child를 클릭했을 때 A가 먼저 찍히고 그 다음 B가 찍히게 된다.
```
```html
<template>
  <div
    class="parent"
    @click.capture.stop="handlerA">
    <div
      class="child"
      @click="handlerB"></div>
  </div>
</template>

<script>
export default {
  methods: {
    handlerA() {
      console.log('A')
    },
    handlerB() {
      console.log('B')
    }
  }
}
</script>
```
```plaintext
수식어 체닝을을 통해 .capture.stop 이렇게 수식어를 작성하게 된다면,
console에는 A만 찍히게 된다.
```

### self
```html
<template>
  <div
    class="parent"
    @click="handlerA">
<!--@click.self="handlerA" -->
    <div
      class="child"></div>
  </div>
</template>

<script>
export default {
  methods: {
    handlerA() {
      console.log('A')
    }
  }
}
</script>
```
```plaintext
위 상황에서  .child를 클릭을 해도 이벤트버블링 때문에 console에는 A가 찍히게 된다.
그걸 방지하고자 할 때 .parent 부분에 self수식어를 작성하게 되면 
.child를 클릭을 해도 console에는 아무것도 찍히지 않는다.
self수식어는 자기 자신의 영역을 정확하게 클릭을 했을 때만 동작을 하게 만들어준다.
```

### @wheel.passive
```html
<template>
  <div
    class="parent"
    @wheel.passive="handler">
    <div class="child"></div>
  </div>
</template>

<script>
export default {
  methods: {
    handler(event) {
      for(let i = 0; i < 10000; i +=1){
        console.log(event)
      }
    }
  }
}
</script>
```
```plaintext
@wheel 이벤트를 사용하면 휠이 움직일 때마다 매소드가 실행되는걸 볼 수 있다. 
일부러 부화를 주기위해 for문을 사용하게 되면 휠이 움직일때마다 10000번 반복하는 부화가 생긴다.
그렇게 되면 스크롤이 버벅거리면서 움직이는걸 볼 수 있다.
그 때 사용하는게 .passive이다.
.passive 기본적인 로직의 처리와 화면의 위아래 스크롤을 완전히 독립시켜줄 수 있다.
```

## 이벤트 핸들링 - 키 수식어
### keydown
```html
<template>
  <input
    type="text"
    @keydown.enter="handler" />
</template>

<script>
export default {
  methods: {
    handler() {
        console.log('Enter!!')
    }
  }
}
</script>
```
```plaintext
@keydown.enter 이렇게 작성을 하고 inputbox에 여러가지 타이핑을 한 뒤
Enter를 누르게되면 메소드가 실행하면서 콘솔창에는 Enter!!가 출력되게 된다.

만약 .enter이 아니라 다른 문자로 작동을 싶으면 뒤에 부분을 원하는 문자로 입력해주고
타이핑 마지막에 그 문자를 입력해주면된다.
ex) keydown.a 라고 입력하면 마지막 문자는 a로 입력해주면 똑같이 메소드가 작동한다.

체이닝 사용도 가능하다.
@keydown.ctrl.a 라고 입력을 하면 마지막에 ctrl를 누른 상태에서 a를 누루면 똑같이 메소드가 자동한다.
```

## 폼 입력 바인딩
### 단방향 데이터 바인딩
```html
<template>
  <h1>{{ msg }}</h1>
  <input
    type="text"
    :value="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world!'
      }
    }
  }
</script>
```
```plaintext
msg의 Hello world라는 데이터를 input요소의 value라는 속성에 연결을 했고
h1요소에 컨텐트라는 요소에 연결을 해서 화면에 출력하고 있다.
그러면 데이터 방향이 한쪽의 방향으로만 화살표가 그려진다. 그래서 input의 입력값을 변경을 해도
h1d의 값은 변함이 없다.
단방향 데이터 바인딩이라고 한다.
```

### 양방향 데이터 바인딩
```html
<template>
  <h1>{{ msg }}</h1>
  <input
    type="text"
    :value="msg"
    @input="handler" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world!'
      }
    },
    methods: {
      handler(event) {
        console.log(event.target.value)
        this.msg = event.target.value
      }
    }
  }
</script>
```
```plaintext
데이터의 흐름
1. msg는 Hello world!가 있는 데이터이고 그것이 input요소의 msg h1요소의 msg로 출력이 되고 있다.
2.input요소를 통해서 사용자가 어떠한 데이터를 입력했을 떄 
  @input이라는 이벤트가 작동이되면서 그 떄 handler라는 메소드가 실행이 된다.
3.handler라는 메소드는 그 내부로 입력된 데이터의 이벤트 객체를 받아서 
  event.target.value 를 다시 msg라는 데이터에다가 할당을 한다.
4.그러면 msg라는 데이터가 갱신이 됐기때문에 그 갱신된 데이터가 다시 msg가 연결되어 있는 각각의 부분의 다시 반영이되서
  반응성을 통해서 h1의 값이 바뀌게 된다.
```
### 양방향 간소화
```html
<template>
  <h1>{{ msg }}</h1>
  <input
    type="text"
    v-model="msg" />
  <!-- :value="msg"
    @input="msg = $event.target.value" -->
  <h1>{{ checkde }}</h1>
  <input
    type="checkbox"
    v-model="checkde" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world!',
        checkde: false
      }
    }
  }
</script>
```
```plaintext
메소드를 활용하는 방식이 아닌 @input="msg = $event.target.value" /> 와 같이
인라인 방식으로 작성을 한다.
더 간소화 하는 방법으로는 v-model="msg" 를 사용한다.
checkde 부분도 양방향으로 설정을 하고 기본값을 false로 지정을 한 뒤
화면에서 체크를 누르면 true가 나오고 체크를 해제하면 false가 나오게 된다.
/////주의사항/////
한글을 입력할 때에 v-model="msg"를 입력하게 되면 처음 한글자가 늦게 반응해서 입력이 안된다.
그래서 한글을 사용 할 때는 @input="msg = $event.target.value"를 사용해야한다.
```

## v-model 수식어

### @change
```html
<template>
  <h1>{{ msg }}</h1>
  <input
    type="text"
    :value="msg"
    @change="msg = $event.target.value" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world!'
      }
    }
  }
</script>
```
```plaintext
@change 수식어를 사용하게되면 사용자가 입력을 하고 있는 동안에는 값이 갱신되지않고
입력을 하고 포커싱이 나가게 되면 그 때 최종적으로 갱신이 된다.
v-model의 사용 방식은 수식어로 lazy를 붙혀준다.
ex)v-model.lazy="msg"
```

### .number
```html
<template>
  <h1>{{ msg }}</h1>
  <!-- <input
    type="text"
    :value="msg"
    @change="msg = $event.target.value" /> -->
  <input
    type="text"
    v-model.number="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 123
      }
    },
    watch: {
      msg(){
        console.log(typeof this.msg)
      }
    }
  }
</script>
```
```plaintext
숫자를 입력해서 양방향이 끝난 뒤에 타입을 보게되면 string로 변경이 되어있다.
그러므로 숫자를 사용 할 때에는 수식어를.number를 붙혀준다.
```
### .trim
```html
<template>
  <h1>{{ msg }}</h1>
  <!-- <input
    type="text"
    :value="msg"
    @change="msg = $event.target.value" /> -->
  <input
    type="text"
    v-model.trim="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world'
      }
    },
    watch: {
      msg(){
        console.log(this.msg)
      }
    }
  }
</script>
```
```plaintext
trim은 앞뒤의 공백을 제거하다. 메소드로 사용을 안하고 수식어로 .trim을 입력해주면 공백제거가 된다.
```