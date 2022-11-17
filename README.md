# Todo List

Recoil 연습

## 문제

- input 을 여러개 만들면 각각의 요소를 핸들링하는 함수를 하나씩 다 정의해줘야 한다.
- 이것을 간단하게 하는 라이브러리: react-hook-form

## 6.6 React-Hook-Form

- 리액트에서 form 으로 작업할 때 좋다.
- form 이 많을 때, form validation 이 많을 떄도 좋다.
- form 은 많은 input 을 가질 수 있따. (이메일, 이름, 성, 비번 등..)

설치: `npm install react-hook-form`

```javascript
// 리액트 훅 적용전
/* function ToDoList() {
  const [toDo, setToDo] = useState("");
  const [toDoError, setToDoError] = useState("");
  const onChange = (event: React.FormEvent<HTMLInputElement>) => {
    const {
      currentTarget: { value },
    } = event;
    setToDoError("");
    setToDo(value);
  };
  const onSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    if (toDo.length < 10) {
      return setToDoError("To do should be longer");
    }
    console.log("submit");
  };
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input onChange={onChange} value={toDo} placeholder="Write a to do" />
        <button>Add</button>
        {toDoError !== "" ? toDoError : null}
      </form>
    </div>
  );
} */
```

### react-hook-form 사용

```javascript
// react-hook-form 적용 이후
import { useForm } from "react-hook-form";

function ToDoList() {
  const { register, watch } = useForm();
  console.log(watch());
  return (
    <div>
      <form>
        <input {...register("email")} placeholder='Email' />
        <input {...register("firstName")} placeholder='First Name' />
        <input {...register("lastName")} placeholder='Last Name' />
        <input {...register("username")} placeholder='Username' />
        <input {...register("password")} placeholder='Password' />
        <input {...register("password1")} placeholder='Password1' />
        <button>Add</button>
      </form>
    </div>
  );
}
```

`const { register, watch } = useForm();`

- regiser 함수는, onChange핸들러, props, setState 같은 애들을 해결해준다.
- register 함수에는 문자열을 넣어줘야 한다.
- register 함수가 반환된 오브젝트를 input props로 넣어주면 된다.

```javascript
console.log(register('toDo'))
/*
{name: 'toDo',
onChange: async event => {...},
onBlur: async event => {...},
ref: ref => {...}}
*/

<input {...register("email")} placeholder='Email' /> // register 함수 사용법
```

### watch 함수

```javascript
console.log(watch());

// {toDo: 'asdfasdf'} 해당 input 에 값을 입력하면 watch 가 추적해준다.
```

### handleSubmit 함수

- handleSubmit 함수는 validation 을 담당한다.
- preventDefault() 도 담당한다.
- handleSubmit 에 전달할 인자는 '유효할 때' 실행될 함수를 넘겨준다.(필수) inValid 일때 실행될 함수도 넘겨줄 수 있다.
- handlesubmit 은 해야하는 모든 validation 이나 다른 일들을 전부 끝마친 후 우리 데이터가 유효할때만 함수를 호출한다.

```javascript
const { handleSubmit } = useForm();
const onValid = (data) => {
  console.log(data);
};

<form onSubmit={handleSubmit(onValid)}></form>;

// 버튼을 눌러 submit이벤트를 발생시킨 후 콘솔에 찍힌 data 오브젝트
/*
Object { email : '1234', firstName:'1123' ,.. }
*/
```

### validation 유효성 검사

```javascript
const { register, watch, handleSubmit } = useForm();
const onValid = (data) => {
  console.log(data);
};

<form onSubmit={handleSubmit(onValid)}>
  <input {...register("email")} required placeholder='Email' />
  <input {...register("email", { required: true })} placeholder='Email' />
  <button>Submit</button>
</form>;
```

- `{required: true}` 를 register 함수 안에다 넣어주면 된다.
- 단순히 태그의 prop으로 `required` 를 넣어주는 것으로 html 에 의지하는 것은 환경에 따라 보호되지 않는다.
- react-hook-form 으로 유효성 체크를 하면 submit 버튼을 눌렀을 때 필요한 인풋으로 focus 도 자동으로 맞춰준다. (사용자경험에도 좋다)

```javascript
// 최소 길이도 정해줄 수 있다.
<input
  {...register("email", { required: true, minLength: 10 })}
  placeholder='Email'
/>
```

### formState 함수 : 예외처리

- 유효하지 않은 submit 을 할 경우 formState 함수가 어떤 에러인지 알려준다

```javascript
const { register, handleSubmit, formState } = useForm();
const onValid = (data) => {
  console.log(data);
};

console.log(formState.errors); // 에러 정보를 담은 object. {} 에러가 없는 경우
<form onSubmit={handleSubmit(onValid)}>
  <input
    {...register("email", { required: true })}
    required
    placeholder='Email'
  />
  <input
    {...register("password", {
      required: { value: 5, message: "your password is too short" },
    })}
    placeholder='password'
  />
  <button>Submit</button>
</form>;
```

### validation 형태

1. `required: true`

```javascript
<input {...register("lastName", { required: true })} placeholder='Last Name' />
```

2. `required: ` 에는 `true` 말고 `문자열`이 들어가도된다
3. `minLength` 에는 `number` 말고 `object` 가 들어가도된다.

```javascript
<input
  {...register("password1", {
    required: "Password is required", //
    minLength: {
      // minLength 에 object가 들어감.
      value: 5,
      message: "Your password is too short.", // 유저에게 보여줄 메세지
    },
  })}
  placeholder='Password1'
/>
```

4. 정규식RegExp 도 사용할 수 있다.

- register 에 pattern 에 해당하는 정보를 넘겨주면 된다.
- /^[A-Za-z0-9._%+-]+@naver.com$/gm 은 네이버 이메일 주소에 해당하는 정규표현식이다.

```javascript
// 네이버 이메일 패턴만 받는 경우
<input
  {...register("email", {
    required: true,
    pattern: /^[A-Za-z0-9._%+-]+@naver.com$/,
    message: "Only naver.com emails allowed",
  })}
  placeholder='Email'
/>
```

5. 유저에게 에러 보여주는 방법

- 유저에게 생길 수 있는 모든 에러를 span 에 넣는다고 해보자.
- 에러마다 서로 다른 typed , 다른 pattern 등이 있어서 다른 에러종류를 가진다. 이것을 간단하게 하는 방법은?

```javascript
// 에러 타입을 일일히 체크해야 되는 코드라서 비추
const {formState:{errors}} = useForm();

<input {...register("email", {required: true, pattern: /^[A-Za-z0-9._%+-]+@naver.com$/, message: "Only naver.com emails allowed"})} placeholder="Email" />
<span>
{errors.email.type === "required" ? "email required"}
</span>
```

- **`requierd: true` 가 아니라 `string`를 넣자!**
- 에러가 있는 경우 에러타입말고 에러메세지만 신경 쓰면 된다.

```javascript
// 추천하는 방법
// 1. required: "에러메세지"
// 2. {error.email.message} 사용
const {formState:{errors}} = useForm();

<input {...register("email", {required: "Email is required", pattern: /^[A-Za-z0-9._%+-]+@naver.com$/, message: "Only naver.com emails allowed"})} placeholder="Email" />
<span>
{errors?.email?.message}
</span>
```

### useForm 디폴트 값 정해주기

- useForm 함수의 인자로 {defaultValue }를 넘겨주자

```javascript
const {
  register,
  handleSubmit,
  formState: { errors },
} = useForm({ defaultValue: { email: "@naver.com" } });
```

## setError 함수

- setError 는 특정한 에러를 발생시키게 해준다.(trigger)
- 특정항목에 대한 에러가 아닌 전체 form 에 대한 에러도 발생시킬수 있다.
- 세번째 인자로 {shouldFocus: true} 를 넣으면 form 에서 사용자가 고른 input 항목에 강제로 focus 시킬수 있다.

```javascript
const { setError } = useForm();

const onValid = (data) => {
  if (data.password !== data.password1) {
    setError(
      "password1",
      { message: "password are not the same" },
      { shouldFocus: true }
    );
  }

  // 특정항목에 대한 에러가 아닌 전체 form 에 대한 에러도 발생시킬수 있다.
  setError("extraError", { message: "server offline" });
};
```

### validate 옵션

- 유효성 검사를 입맛대로 커스텀 하고 싶을 때 validate 옵션을 사용한다.
- validate 옵션은 함수를 값으로 가진다 `validate: (value) => boolean`
- false 면 validation 을 통과하지 못해서 에러를 발생시킨다. boolean 대신 string을 넣어서 에러메세지로 대체 가능
- 여러 유효성검사 조건들을 중복해서 쓸수 있다.

```javascript
<input
  {...register("firstName", {
    required: "write here",
    validate: {
      noNico: (value) => (value.includes("nico") ? "no nicos allowed" : true),
      noNick: (value) => (value.includes("nick") ? "no nick allowed" : true),
    },
  })}
  placeholder='First Name'
/>
```

### setValue 함수

- submit 을 한 이후 input 의 내용을 비워주고 싶을 때 사용한다.

```javascript
const {handleSubmit, setValue} = useForm();

const handleValid = (data) => {
  console.log(data.toDo);
  setValue("toDo", "") // toDo 항목인 form 의 값을 빈문자열""로 설정
}
return (
  <form onSubmit= {handleSubmit(handleValid)}>
  <input
  {...register("toDo", {
    required: "please write a todo",
  })}
  placeholder="write a todo"
  >
  <button>submit</button>
  </form >
)
```

## 6.11 Add to do

## Recoil 사용하기

- recoil 의 atom 데이터에 접근하려면 useRecoilValue 함수를 사용한다. 인자는 atom 를 넘겨준다.
- atom 의 값을 변경하려면 modifier 함수를 사용한다.
- useRecoilState 훅을 이용하면 더 간단한 코드

```javascript
import {atom} from "recoil"
const toDoState = atom({
  key: "toDo",
  default: [],
})

function ToDoList(){
  const value = useRecoilValue(toDoState) // [] 값이 할당된다.
  const modFn = useSetRecoilState(toDoState) // value를 수정할 수 있는 함수. 값 이나 함수를 넣자!

  // 더 간편한 방법 (추천)
  const [value modFn] = useRecoilState(toDoState)
}
```

## 4.13 Categories

###

```javascript
// DOING 카테고리에 속하면 버튼이 안보이고, 안속하면 버튼이 보인다.
{
  category !== "DOING" && (
    <button name='DOING' onClick={onClick}>
      Doing
    </button>
  );
}
```

```javascript
export interface IToDo {
  text: string;
  id: number;
  category: "TO_DO" | "DOING" | "DONE";
}
// 에서
const onClick = (newCategory: "TO_DO" | "DOING" | "DONE") => {
  console.log(newCategory);
};
// 보다는 아래를 추천 (효과는 동일)
const onClick = (newCategory: IToDo["category"]) => {
  console.log(newCategory);
};
```

### 버튼에 이름 부여해서 함수 하나로 핸들링하기

```javascript
function ToDo({ text, category, id }: IToDo) {
  const setToDos = useSetRecoilState(toDoState);
  const onClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    const {
      currentTarget: { name },
    } = event; // event.currentTarget.name 에 클릭한 버튼의 name 값들이 담겨있다.
  };
}
<li>
  <span>{text}</span>
  {category !== "DOING" && (
    <button name='DOING' onClick={onClick}>
      Doing
    </button>
  )}
  {category !== "TO_DO" && (
    <button name='TO_DO' onClick={onClick}>
      To Do
    </button>
  )}
  {category !== "DONE" && (
    <button name='DONE' onClick={onClick}>
      Done
    </button>
  )}
</li>;
```

## immutability "배열의 원소를 어떻게 교체할까?"

원소를 교체하는 이유는 원소의 위치가 바뀌지 않길 바라기 때문

### category 수정해보기

1. 수정하고 싶은 todo를 찾는 방법: id를 이용한다.
2. 수정하고자 하는 todo의 경로를 알아야 한다. : 타겟의 index를 알아야 한다.
3. slice 메소드 이용하면 간단

```javascript
function ToDo({ text, category, id }: IToDo) {
  const setToDos = useSetRecoilState(toDoState);
  const onClick = (event) => {
    setToDos((oldToDos) => {
      // targetIndex 를 알아냄
      const targetIndex = oldToDos.findIndex((toDo) => toDo.id === id);
      const oldToDo = oldToDos[targetIndex];
      const newToDo = { text, id, category: name };

      return [
        oldToDos.slice(0, targetIndex),
        newToDo,
        ...oldToDo.slice(targetIndex + 1),
      ];
    });
  };
}
```

## Selector (from recoil)

- selector 는 derived state를 나타낸다.
- state 를 입력받아서 그걸 변형해 반환하는 순수함수를 거쳐 반환된 값을 말한다.
- 즉 state 를 가져와서 다른 state 를 만들 수 있다.
- atom 은 단순히 데이터를 주고 selector 가 그걸 변환해서 반환한다.
- selector 는 atom 을 보고있다가. atom 이 변하면 selector도 변한다.
- 사용 예시

* 카테고리에 상관없이 모든 todo가 배열에 담겨 있다고 하자.
* 각 카테고리마다 atom를 만들지 않고 하나의 atom만 쓰고 싶다.
* selector 를 이용하면 이 todo들을 분류할 수 있다!

### Selector 사용하기

```javascript
// atoms.tsx
import {selector} from 'recoil';
export const toDoSelector = selector({
  key: "toDoSelector"
  get: ({get}) => {
    const toDos = get(toDoState);

    return [toDos.filter((toDo) => toDo.category === "TO_DO"),
            toDos.filter((toDo) => toDo.category === "DOING"),
            toDos.filter((toDo) => toDo.category === "DONE")];
  }
})

// ToDoList.tsx
import { useRecoilValue } from "recoil";
function ToDoList(){
  const selectorOutput = useRecoilValue(toDoSelector);
  const [toDos, doing, done] = useRecoilValue(toDoSelector);
}
```

- selector() 에는 {key} 가 전달되어야 한다!
- selector() 에는 get함수가 전달되어야 한다.

* get함수는 object를 인자로 받는데 object 안에는 get 함수가 들어있다. 이 함수가 selector 로 atom 을 가져올 수 있다.

- selector는 useRecoilValue 함수에 넣어준다.

#### select 와 option 으로 필터링 하기

```javascript
// atoms.tsx
export const toDoSelector = selector({
  key: "toDoSelector",
  get: ({ get }) => {
    const toDos = get(toDoState);
    const category = get(categoryState);
    // category 에 따라서 todo 리스트를 반환한다
    return toDos.filter((toDo) => toDo.category === category);
  },
});
// ToDoList.tsx
function ToDoList() {
const toDos = useRecoilValue(toDoSelector);
const [category, setCategory] = useRecoilState(categoryState);
const onInput = (event: React.FormEvent<HTMLSelectElement>) => {
  setCategory(event.currentTarget.value);
};

return (
<select value={category} onInput={onInput}>
  <option value='TO_DO'>To Do</option>
  <option value='DOING'>Doing</option>
  <option value='DONE'>Done</option>
</select>
<CreateToDo />

{toDos?.map((toDo) => (
  <ToDo key={toDo.id} {...toDo} />
))}
)
}
```
