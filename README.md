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

<style scoped>
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