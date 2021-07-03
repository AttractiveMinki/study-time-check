### 210616 intro

드디어! 프로젝트를 위한 폴더 만듦

vue create study_time

기본적인 설정 + 깨작



### 210621 2일차

vuex 사용 설정

npm install vuex --save



메인 페이지(App) 구현

- hour, minute 입력받기



vuex 구현

- actions - addHour 만들기
- mutations - ADD_HOUR 만들기
- state - totalHour, totalMinute 만들기



안내 문구 띄우기 성공, 입력 버튼 및 입력창 만들기 성공

총 시간 + 총 분 띄우기 실패

##### TypeError: Cannot read property 'state' of undefined

Vuex에서 state를 읽어오지 못하는 듯..

`{{ totalHour }}시간 {{ totalMinute }}분 공부했습니다.` 이 문장을 포함하면 페이지 출력이 없음.



state 못쓰는김에 hour, minute 입력은 잘 들어오는지 확인해보자~ 하고 해보니 안됨.ㅋㅋㅋㅋㅋ

##### TypeError: Cannot read property 'dispatch' of undefined

모르긴 몰라도 addHour([hour, minute]) 이 부분이 문제인듯.

vuex의 addHour 안에서 console.log(data)를 찍어보았지만, 콘솔 창에 아무 것도 뜨지 않음

-> addHour도 들어가지 못하고 있다.



210621 마무리

오랜만에 Vue 보니까 낯설다.

final_pjt가 끝난 뒤, intro를 만들면서 처음 Vue를 복습한 것 같다.

이후 두 번째로 Vue를 보고 있는데, 제법 감이 돌아왔다.

actions, mutations, state 등의 개념은 단어만 보고도 곧바로 떠올랐다.

다면 commit, dispatch 등의 개념을 기억하기 위해 약간의 구글링이 필요했다.

vuex에 대한 회상은 아직 완전하지 않은 것 같다. 그러지 않고서야 에러를 몇 개씩 범할 리가 없다. ㅠㅠ

조금씩 꾸준하게 하자! 세상에 도움을 주는 코드를 짜자!



### 210622 화 3일차

TypeError: Cannot read property 'state' of undefined

"TypeError: Cannot read property 'dispatch' of undefined"

구글링을 해보니, vue와 vuex가 연결되어 있지 않은 것 같다.



해결

https://parksrazor.tistory.com/211

https://stackoverflow.com/questions/52482814/cannot-read-property-dispatch-of-undefined-in-vuex



main.js에 다음을 추가

```js
// import router from './router'
import store from './store' // 이거 추가

Vue.config.productionTip = false

new Vue({
  // router,
  store, // 이거도 추가
  render: h => h(App),
}).$mount('#app')

```

라우터는 추가해야 하지만 아직 폴더를 만들지 않았으므로 추가하지 않음.



root instance에 store를 추가하지 않았기 때문에 발생한 문제.

제일 최상단은 App.vue로 배웠는데 이보다 위에 main.js가 있는 듯.

기존에 짰던 Vue 코드를 참고하여 해결

역시 사이드 플젝 해본 사람이 더 많이 알게 되는 것 같다.



```vue
<div>
    <input type="number" v-model="information.hour" required>
    <input type="number" v-model="information.minute" required @keypress.enter="addHour([information])">
    <button @click="addHour([information])">add</button>
</div>
```

- 입력해준 값이 undefined로 찍히는 문제 발견
- input type="number"로 수정 - 옳은 방향이지만 문제를 고치진 못함.
- required 옵션을 넣었기 때문에 빈 값이면 값이 들어갈 수 없는데, 들어가는 문제 발견

```python
// 기존
  data: function () {
    return {
      information: {
        hour: 0,
        minute: 0,
      }
    }
  },
// 수정
  data: function () {
    return {
      information: {
        hour: 0,
        minute: 0,
      }
    }
  },
```

- 위와 같이 변경. 여전히 undefined가 들어감.



- 해결

information[0].hour, information[0].minute로 찍으니까 해결

[0]이 왜 붙는걸까?

장고랑 뷰 같이 쓸때는 그냥 axios에 data 실어 보내줬는데, 장고에선 알아서 거른 것 같다.



시간 넣고 몇 시간 공부했는지 체크하기

01시간 23분, 2시간 13분 넣기

=> '00121시간 2253분 공부했습니다.'

...

덧셈을 하지 않고, 문자로 인식해서 뒤에 append하는 것 같다.

구글링을 통해 parseInt를 알게 되었고 다음과 같이 코드를 수정하였다.

python은 그냥 int 붙이면 되는데, JavaScript는 int도 못쓰고 type도 못써서 불편하다.



기존

```javascript
    ADD_HOUR: function (state, information) {
      state.totalHour += information[0].hour
      state.totalMinute += information[0].minute
      if (state.totalMinute > 60)
      {
        state.totalMinute -= 60
        state.totalHour += 1
      }
      console.log(state.totalHour, state.totalMinute)
    },
```

변경

```javascript
    ADD_HOUR: function (state, information) {
      let temp_hour = parseInt(state.totalHour) + parseInt(information[0].hour)
      let temp_minute = parseInt(state.totalMinute) + parseInt(information[0].minute)
      // parseInt(state.totalHour) += parseInt(information[0].hour)
      // parseInt(state.totalMinute) += parseInt(information[0].minute)
      state.totalHour = temp_hour
      state.totalMinute = temp_minute
      if (state.totalMinute > 60)
      {
        state.totalMinute -= 60
        state.totalHour += 1
      }
      console.log(state.totalHour, state.totalMinute)
    },
```





개선사항

시작 시간, 종료 시간을 입력하면 자동으로 시간 계산 및 총 공부 시간 출력하기

빈 값을 넣으면 값을 넣으라는 알림 창이 나와야 하는데(required 썼쥬), NaN 값이 들어가고 전체 값이 깨지는 현상 발생.



빈 값 넣으면 전체 시간이 깨지는 문제

해결

required가 왜 동작하지 않는 지는 연구중.

일단 빈 값이 들어오면 ADD_HOUR로 commit하지 못하도록 설정.

기존

```javascript
    addHour: function ({ commit }, information) {
      console.log(information)
      console.log(information[0].hour)
      console.log(information[0].minute)
      console.log('안뇽')
      commit('ADD_HOUR', information)
    },
```



변경

```javascript
    addHour: function ({ commit }, information) {
      console.log(information)
      console.log(information[0].hour)
      console.log(information[0].minute)
      console.log('안뇽')
      if (information[0].hour != "" && information[0].minute != "")
      {
        commit('ADD_HOUR', information)
      }
    },
  },
```



오늘 한 일

vuex, vue 연결하기

현재 공부시간 입력 후 공부 시간 체크 기능

60분 이상 -> 시간 += 1, 분 -= 60 설정



앞으로 할 일

시작 시간과 종료 시간 입력으로 현재 공부시간, 총 공부시간 계산해주기

TodoList처럼 공부 시간 아래로 쭉~ 공부 시간 보이게 하기, CRUD

Local Storage에 공부 시간 저장하기 or 장고와 연결해서 저장해주기

배포





### 210628 월 4일차

앞으로 할 일

시작 시간과 종료 시간 입력으로 현재 공부시간, 총 공부시간 계산해주기

TodoList처럼 공부 시간 아래로 쭉~ 공부 시간 보이게 하기, CRUD

Local Storage에 공부 시간 저장하기 or 장고와 연결해서 저장해주기

배포 (netlify)



오늘 한 일

시작 시간과 종료 시간 입력으로 현재 공부시간, 총 공부시간 계산해주기

TodoList처럼 공부 시간 아래로 쭉~ 공부 시간 보이게 하기, CR



문제점

시작 시간과 종료 시간을 입력할 때, 12:20 ~ 13:10와 같이 입력할 수도 있고, 12 20 13 10처럼 입력할 수도 있다.

해결

replace를 통해 ' '를 ':'로 바꾸어준다. 시간은 str 형식으로 일괄 입력받고, vuex에서 시간과 분을 나눈다.

```js
    CALC_HOUR: function (state, information) {
      console.log(information[0])
      // let temp_hour1 = information[0].start_time.split(':').split(' ')
      // let temp_hour2 = information[0].end_time.split(':').split(' ')

      let temp_hour1 = information[0].start_time.replace(' ', ':').split(':')
      let temp_hour2 = information[0].end_time.replace(' ', ':').split(':')
      
      if (temp_hour1[0] > 24 || temp_hour2[0] > 24) {
        alert('시간은 24보다 작아야 합니다.')
        return
      }
      if (temp_hour1[1] > 60 || temp_hour2[0] > 60 ) {
        alert('분은 60보다 작아야 합니다.')
        return
      }

      let plus_hour = parseInt(temp_hour2[0]) - parseInt(temp_hour1[0])
      let plus_minute = parseInt(temp_hour2[1]) - parseInt(temp_hour1[1])

      if (plus_minute < 0) {
        plus_minute += 60
        plus_hour -= 1
      }

      if (plus_hour < 0) {
        plus_hour += 24
      }
      
      state.totalHour += plus_hour
      state.totalMinute += plus_minute

      state.time_history.push(temp_hour1[0] + ':' + temp_hour1[1] + ' ~ ' + temp_hour2[0] + ':' + temp_hour2[1] + ' ' + plus_hour + ':' + plus_minute)
      
      if (state.totalMinute >= 60)
      {
        state.totalMinute -= 60
        state.totalHour += 1
      }
      console.log(state.totalHour, state.totalMinute)
    },
```



문제점: 11:50 ~ 10:50을 하면 공부 시간이 음수가 된다.

해결: 연산을 통해 분이 음수가 되면 60을 더해주고(시간은 1을 빼준다.), 시간이 음수가 되면 24를 더해준다.



문제점: TodoList처럼 화면에 공부 시간이 보이도록 하고 싶다.

해결: v-for를 이용하여 한 줄씩 화면에 보이도록 하였다.

```vue
<template>
  <div>
    <div>
      <div style="font-size:30px">Study Time Check Web Page</div>
      <hr>
      <div style="font-size:24px">
        총 {{ totalHour }}시간 {{ totalMinute }}분 공부했습니다.
      </div> 
      <div>
        <br>
        <br>
        <div>
          <div style="font-size:30px">내가 공부한 시간 넣어서 계산하기</div>
          <br>
          <label for="hour"></label>
          <input type="number" v-model="information.hour" required>시간
          <label for="minute"></label>
          <input type="number" v-model="information.minute" required @keypress.enter="addHour([information])">분
          <button @click="addHour([information])">add</button>
        </div>
      </div>
      <br>
      <br>
      <div>
        <div style="font-size:30px">시작 시간과 종료 시간 입력해서 계산하기</div>
        <br>
        <div>아래와 같이 입력해주시기 바랍니다.</div>
        <div>ex) 12:20 ~ 13:10 혹은 12 20 13 10</div>
        <br>
        <label for="start_time"></label>
        <input type="text" v-model="time_information.start_time" required> ~ 
        <label for="end_time"></label>
        <input type="text" v-model="time_information.end_time" required @keypress.enter="calcHour([time_information])"> 
        <button @click="calcHour([time_information])">add</button>
      </div>
    </div>
    <div>
      <div v-for="(time, idx) in time_history" :key="idx">
        {{ time }}
        <!-- {{ time }} -->
      </div>
      {{ totalHour }}:{{ totalMinute }}
    </div>
    
  </div>
</template>
```



문제: += 연산을 사용하니 리스트에 문자열 하나하나씩 들어간다. append를 사용해도 마찬가지다.

```vue
      state.time_history.append(temp_hour1 + ' ~ ' + temp_hour2)
혹은
      state.time_history += (temp_hour1 + ' ~ ' + temp_hour2)
```



해결: push 연산을 사용한다.

```js
      state.time_history.push(temp_hour1[0] + ':' + temp_hour1[1] + ' ~ ' + temp_hour2[0] + ':' + temp_hour2[1] + ' ' + plus_hour + ':' + plus_minute)
```



앞으로 할 일

TodoList처럼 공부 시간 아래로 쭉~ 공부 시간 보이게 하기, UD

Local Storage에 공부 시간 저장하기

배포 (netlify)



6/29

사용중 불편한 사항

- .zfill() 추가해주세요
  - 2:4 -> 02:04처럼
- 시간 초기화 버튼 추가해주세요
  - 초기화버튼 누르면 vuex 시간 초기화
- 시간 입력 후에 다 지워주세요
- 엔터를 누른 뒤 값이 입력되면 두 번째 칸에서 첫 번째 칸으로 커서를 옮겨주세요
- 23:32, 23 32 -> 2332로 입력받아도 동작하게 만들기.





### 210630 수 5일차

github.io를 통해 배포를 시도하였다. 결과적으로 대실패. ㅜㅜ

내가 생각한 실패의 원인

- Vue2로 시작해서 그런 것 같다. 배포가 성공적으로 마무리되었을 때는 Vue2, Vue3이 아닌 다른 것을 골랐다.

- 혹은, 파일의 경로가 잘못되어 그런 것 같다. ㅠㅠ
  - 기존: /study_time_check_web/
  - 변경: /ssafy5-study/study_time_check_web/study_time/
- 결과적으로 실패했다.

ㅠㅠ



