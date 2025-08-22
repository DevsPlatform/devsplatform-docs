# React Hook Form 완전 가이드

**기존 방식의 문제점:**

- useState로 각 입력값 관리 → 코드가 길어짐
- 유효성 검사를 직접 구현 → 복잡함
- 리렌더링이 많이 발생 → 성능 저하

**React Hook Form의 장점:**

- 코드가 간단해짐
- 성능이 좋음 (리렌더링 최소화)
- 유효성 검사가 쉬움
- TypeScript 지원 완벽

---

## 🔧 설치 및 기본 설정

```bash
npm install react-hook-form

```

```tsx
import { useForm } from 'react-hook-form';

// 타입 정의 (TypeScript)
interface FormData {
  name: string;
  email: string;
}

```

---

## 🎛️ 핵심 훅들 이해하기

### 1. useForm()

폼을 만들기 위한 메인 훅

```tsx
const {
  register,     // 입력 필드 등록하는 함수
  handleSubmit, // 폼 제출 처리하는 함수
  watch,        // 입력값 실시간으로 보는 함수
  formState,    // 폼 상태 (에러, 유효성 등)
} = useForm<FormData>();

```

### 2. register

**역할:** 입력 필드를 폼에 연결
**기존 방식 vs Hook Form:**

```tsx
// ❌ 기존 방식 (복잡함)
const [name, setName] = useState('');
<input
  value={name}
  onChange={(e) => setName(e.target.value)}
/>

// ✅ Hook Form (간단함)
<input {...register("name")} />

```

### 3. handleSubmit

**역할:** 폼이 제출될 때 실행되는 함수

```tsx
const onSubmit = (data) => {
  console.log(data); // {name: "홍길동", email: "test@test.com"}
};

<form onSubmit={handleSubmit(onSubmit)}>
  {/* 폼 내용 */}
</form>

```

### 4. watch

**역할:** 입력값을 실시간으로 감시

```tsx
const nameValue = watch("name");        // 특정 필드만
const [name, email] = watch(["name", "email"]); // 여러 필드
const allValues = watch();              // 모든 필드

// 사용 예시
<p>입력한 이름: {nameValue}</p>

```

### 5. formState

**역할:** 폼의 현재 상태 확인

```tsx
const { errors, isValid, isDirty } = formState;

// errors: 유효성 검사 에러들
// isValid: 폼이 유효한지
// isDirty: 사용자가 입력을 했는지

```

---

## ✅ 유효성 검사 (Validation)

### 기본 규칙들

```tsx
<input
  {...register("email", {
    required: "이메일은 필수입니다",           // 필수 입력
    minLength: {                           // 최소 길이
      value: 5,
      message: "5글자 이상 입력하세요"
    },
    maxLength: {                           // 최대 길이
      value: 20,
      message: "20글자 이하로 입력하세요"
    },
    pattern: {                             // 정규식
      value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
      message: "올바른 이메일 형식이 아닙니다"
    }
  })}
/>

```

### 숫자 검증

```tsx
<input
  type="number"
  {...register("age", {
    required: "나이는 필수입니다",
    min: {                                 // 최솟값
      value: 1,
      message: "1 이상이어야 합니다"
    },
    max: {                                 // 최댓값
      value: 120,
      message: "120 이하여야 합니다"
    }
  })}
/>

```

### 커스텀 검증

```tsx
<input
  {...register("password", {
    required: "비밀번호는 필수입니다",
    validate: {
      hasNumber: (value) =>
        /\d/.test(value) || "숫자를 포함해야 합니다",
      hasSpecial: (value) =>
        /[!@#$%^&*]/.test(value) || "특수문자를 포함해야 합니다"
    }
  })}
/>

```

### 에러 메시지 표시

```tsx
{errors.email && (
  <p style={{color: 'red'}}>
    {errors.email.message}
  </p>
)}

```

---

## 🔄 실전 예제들

### 1. 간단한 로그인 폼

```tsx
function LoginForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => {
    console.log('로그인 데이터:', data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("email", {
          required: "이메일을 입력하세요",
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: "올바른 이메일을 입력하세요"
          }
        })}
        placeholder="이메일"
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register("password", {
          required: "비밀번호를 입력하세요",
          minLength: {
            value: 6,
            message: "6글자 이상 입력하세요"
          }
        })}
        placeholder="비밀번호"
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit">로그인</button>
    </form>
  );
}

```

### 2. 비밀번호 확인 폼

```tsx
function SignupForm() {
  const { register, handleSubmit, watch, formState: { errors } } = useForm();

  const password = watch("password"); // 비밀번호 실시간 감시

  const onSubmit = (data) => {
    console.log('회원가입 데이터:', data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        type="password"
        {...register("password", {
          required: "비밀번호를 입력하세요",
          minLength: {
            value: 8,
            message: "8글자 이상 입력하세요"
          }
        })}
        placeholder="비밀번호"
      />
      {errors.password && <p>{errors.password.message}</p>}

      <input
        type="password"
        {...register("confirmPassword", {
          required: "비밀번호 확인을 입력하세요",
          validate: (value) =>
            value === password || "비밀번호가 일치하지 않습니다"
        })}
        placeholder="비밀번호 확인"
      />
      {errors.confirmPassword && <p>{errors.confirmPassword.message}</p>}

      <button type="submit">회원가입</button>
    </form>
  );
}

```

---

## ⚙️ 고급 옵션들

### 유효성 검사 모드

```tsx
const form = useForm({
  mode: 'onChange',    // 입력할 때마다 검사
  mode: 'onBlur',      // 포커스 벗어날 때 검사
  mode: 'onSubmit',    // 제출할 때만 검사 (기본값)
});

```

### 기본값 설정

```tsx
const form = useForm({
  defaultValues: {
    name: "홍길동",
    email: "test@example.com"
  }
});

```

### 폼 초기화

```tsx
const { reset } = useForm();

// 빈 값으로 초기화
reset();

// 특정 값으로 초기화
reset({
  name: "새이름",
  email: "new@example.com"
});

```

---

## 💡 실용적인 팁들

### 1. 버튼 비활성화

```tsx
const { formState: { isValid } } = useForm({ mode: 'onChange' });

<button disabled={!isValid} type="submit">
  제출
</button>

```

### 2. 로딩 상태 관리

```tsx
const [isLoading, setIsLoading] = useState(false);

const onSubmit = async (data) => {
  setIsLoading(true);
  try {
    await submitAPI(data);
  } finally {
    setIsLoading(false);
  }
};

<button disabled={isLoading} type="submit">
  {isLoading ? '처리중...' : '제출'}
</button>

```

### 3. 조건부 유효성 검사

```tsx
const isEmailRequired = watch("contactMethod") === "email";

<input
  {...register("email", {
    required: isEmailRequired ? "이메일은 필수입니다" : false
  })}
/>

```

---

## 🚨 자주 하는 실수들

### 1. register 잘못 사용하기

```tsx
// ❌ 잘못된 방법
<input {...register} />

// ✅ 올바른 방법
<input {...register("fieldName")} />

```

### 2. 에러 처리 누락

```tsx
// ❌ 에러 무시
<input {...register("email", { required: true })} />

// ✅ 에러 표시
<input {...register("email", { required: "이메일은 필수입니다" })} />
{errors.email && <p>{errors.email.message}</p>}

```

### 3. handleSubmit 빼먹기

```tsx
// ❌ 직접 onSubmit 사용
<form onSubmit={onSubmit}>

// ✅ handleSubmit으로 감싸기
<form onSubmit={handleSubmit(onSubmit)}>

```

---

## 📚 자주 사용하는 패턴들

### 이메일 유효성 검사

```tsx
const emailValidation = {
  required: "이메일을 입력해주세요",
  pattern: {
    value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
    message: "올바른 이메일 형식을 입력해주세요"
  }
};

<input {...register("email", emailValidation)} />

```

### 휴대폰 번호 유효성 검사

```tsx
const phoneValidation = {
  required: "휴대폰 번호를 입력해주세요",
  pattern: {
    value: /^01[0-9]{1}-?[0-9]{4}-?[0-9]{4}$/,
    message: "올바른 휴대폰 번호를 입력해주세요"
  }
};

<input {...register("phone", phoneValidation)} />

```

### 비밀번호 강도 검사

```tsx
const passwordValidation = {
  required: "비밀번호를 입력해주세요",
  minLength: {
    value: 8,
    message: "비밀번호는 8자리 이상이어야 합니다"
  },
  pattern: {
    value: /^(?=.*[a-zA-Z])(?=.*[0-9])(?=.*[!@#$%^&*])/,
    message: "영문, 숫자, 특수문자를 포함해야 합니다"
  }
};

<input type="password" {...register("password", passwordValidation)} />

```

---

## 🎨 실제 프로젝트 적용 예시

### 비밀번호 찾기 폼

```tsx
function FindPasswordForm() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors, isValid }
  } = useForm({ mode: 'onChange' });

  const [isCodeSent, setIsCodeSent] = useState(false);

  const name = watch("name");
  const email = watch("email");

  const sendCode = async () => {
    if (name && email && !errors.email) {
      // API 호출
      setIsCodeSent(true);
    }
  };

  const onSubmit = (data) => {
    console.log('폼 데이터:', data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("name", {
          required: "이름을 입력해주세요"
        })}
        placeholder="이름"
      />
      {errors.name && <p>{errors.name.message}</p>}

      <input
        {...register("email", {
          required: "이메일을 입력해주세요",
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: "올바른 이메일을 입력해주세요"
          }
        })}
        placeholder="이메일"
      />
      {errors.email && <p>{errors.email.message}</p>}

      <button
        type="button"
        onClick={sendCode}
        disabled={!name || !email || !!errors.email}
      >
        인증번호 전송
      </button>

      {isCodeSent && (
        <input
          {...register("verificationCode", {
            required: "인증번호를 입력해주세요"
          })}
          placeholder="인증번호"
        />
      )}

      <button disabled={!isValid} type="submit">
        비밀번호 찾기
      </button>
    </form>
  );
}

```

---

## 🔗 유용한 링크들

- [공식 문서](https://react-hook-form.com/)
- [API 레퍼런스](https://react-hook-form.com/api)
- [예제 모음](https://react-hook-form.com/get-started)

---

## 📝 정리

**React Hook Form의 핵심:**

1. `useForm()` 훅으로 시작
2. `register()`로 입력 필드 연결
3. `handleSubmit()`으로 폼 제출 처리
4. `errors`로 에러 표시
5. `watch()`로 실시간 값 감시

**기억할 점:**

- 기존 useState 방식보다 훨씬 간단
- 성능이 좋음 (리렌더링 적음)
- TypeScript와 완벽 호환
- 유효성 검사가 쉬움
