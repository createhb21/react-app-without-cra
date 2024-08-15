# react-app-without-cra

## commit -m "before babel setup"

예상했던 것과는 달리 `Hello, React!`는 보이지않으며
콘솔창에서는 `Uncaught SyntaxError: Unexpected token '<'`라는 에러가 보여진다.

왜 Syntax Error가 발생했을까?
Syntax Error란, 스크립트에 문법적인 오류가 있다는 것을 의미한다.

React를 사용해봤다면 스크립트 중간에 태그를 <>를 이용한 형태로 사용하는 것이 전혀 이상하지 않겠지만, 사실 이것은 정통적인 자바스크립트 문법이 아니다.
즉, 이것들은 JSX라고 하는 코드 가독성 향상을 위해 기존 Javascript을 확장한 새로운 문법이다.

### JSX 문법 없이 React 앱을 개발할 수 있을까?

그렇다면 현재로써는 JSX 문법을 사용할 수 있는 방법이 없어보인다.
따라서 JSX 없이 컴포넌트를 생성해야 하는데,
다행히 React에서 createElement라는 함수를 통해 이를 지원하고 있다.

## commit -m "before babel setup - with createElement"

createElement의 첫번째 파라미터에 태그를 정의해주고,
두번째 파라미터에 원래는 props를 넘길 수 있게 되어 있는데 현재는 넘길 게 없으니 null로 전달해주자.
마지막으로 children을 정의해주면 되는데, 현재는 `Hello, React!` 라는 textContent만 필요하기 때문에, 이를 전달해주면 된다.

그런 다음, ReactDOM의 render 함수를 통해 App 컴포넌트를 root라는 id를 가진 태그 안에 rendering해주면, 원하는대로 h1태그가 잘 렌더링 되는 것을 확인할 수 있다.

여기까지 babel과 webpack없이 아주 기초적인 React 앱을 구성해보았다.

JSX 없이도 앱을 만들 수는 있지만, 어플리케이션의 복잡도가 올라갈수록
이러한 방식으로 컴포넌트를 정의하기는 굉장히 불편해보인다.

그럼 어떻게 해야 JSX 문법을 사용할 수 있을까?
여기서 등장하는 것이 바로 트랜스파일러(Babel 등)이다.

앞으로 다루게 될 Babel이 트랜스파일러에 해당하는데,
쉽게 말하면 최신 문법의 자바스크립트를 구버전의 문법으로 바꿔주는 역할을 한다.
여기서 말하는 최신 문법에는 JSX같은 확장 문법도 포함된다.

## commit -m "with Babel StandAlone"

자 이제 Babel을 적용한 다음, createElement의가 아닌 JSX로 React앱을 바꿔보자.
그 전에 트랜스파일러의 개념에 대해 다시 짚고 넘어가자.
이전 커밋 히스토리에서 Babel과 같은 트랜스파일러를 문법 변환 도구로 소개했었는데
엄밀히 말하면, 트랜스파일러는 단순히 문법 변환만 하는 것이 아니라 다른 언어로 변경해주는 역할도 수행한다.

예를 들면, Typescript같은 superset 언어들을, Javascript로 변경해주기도한다.

> superset: 기존 언어에 확장 기능을 추가하여 개발된 언어를 의미한다. Typescript의 경우 Javascript에 정적 타입 검사 기능을 제공한다.

왜냐하면, Javascript 엔진 자체만으로는 Typescript의 실행이 불가능하기 때문이다.

JSX의 경우는 superset 언어라기보단 Javascript의 확장 문법에 가까운데, 이 역시 자바스크립트 엔진으로 실행하려면 순수 자바스크립트 문법으로 변경해줘야 한다.
이때 사용되는 것이 바로 트랜스파일러이다.

이번에 다루게 될 Babel도 트랜스파일러 도구 중 하나이다.
자 그럼 이제 현재까지 작성한 코드에 Babel을 추가하여 JSX 문법으로 고쳐보도록 하자.

마찬가지로, Babel 기능 적용을 위해 CDN을 이용할건데, unpkg.com 사이트에 접속하여
Babel의 standalone 버전을 가져오도록 하자.

> babel stanalone: Babel을 브라우저 환경에서 직접 사용할 수 있도록 만들어진 버전으로, 주로 Babel 동작 테스트용으로 사용한다.

Babel 패키지가 정상적으로 로드되는지 확인되었다면, 이제 script를 JSX 문법으로 바꿔주면 되겠다.
JSX 문법을 사용할 수 있다는 것은 createElement 함수를 쓸 필요없이 태그 작성하듯이 코드를 작성할 수 있다는 뜻이다.

```typescript
const App = () => <h1>Hello, React!</h1>;
ReactDOM.render(<App />, document.getElementById("root"));
```

자 이제 코드를 실행시켜 브라우저에서 확인해보자.

하지만 예상과는 달리, Babel을 적용했는데도 여전히 동일한 `Uncaught SyntaxError: Unexpected token '<'` 에러가 발생하고 있다.

왜 이런 현상이 발생하는 것일까?

바로 브라우저에게 어떤 스크립트를 Babel로 변환하고 싶은지 알려주지 않았기 때문이다.

script 태그에 type 프로퍼티를 추가하여 "text/babel" 옵션을 주면 깔끔하게 해결할 수 있다.
이 옵션을 통해 브라우저에게 "이 스크립트는 Babel로 트랜스파일한 다음 실행시켜줘"라고 알려줄 수 있는 것이다.

다시 실행하면, 정상적으로 h1태그가 잘 렌더링되는 것을 확인할 수 있다.

그런데, 콘솔창에 한가지 에러가 발생하고 있는데,
`You are using the in-browser Babel transformer. Be sure to precompile your scripts for production`
내용을 해석하면, 브라우저에서 스크립트를 실시간으로 트랜스파일하지 말고 미리 트랜스파일된 스크립트를 실행시키라는 의미이다.

그럼 이제 이 에러를 해결해보자.
여기에 적용할 수 있는 도구가 바로 Webpack과 같은 번들러이다.

## commit -m "Babel on build process"

`You are using the in-browser Babel transformer. Be sure to precompile your scripts for production` 에러는
성능적인 이슈로 인해 발생하는 에러였다.

이 에러를 해결하기 위해, 빌드 타임에서 트랜스파일 할 수 있도록 수정해보자.

이를 위해, 먼저 npm을 이용하여 라이브러리 3개를 설치하자.
`@babel/cli`와 `@babel/core`는 빌드 타임에서 Babel을 실행시키기 위한 도구이고,
`@babel/preset-react`는 react 문법을 javascript로 변환시키기 위한 도구이다.
쉽게 말해, 이 preset을 통해 Babel에게 "이런 문법은 이렇게 변환하면 돼"라고 알려주는 것이다.

이전에 사용했던 standalone 버전도 이 preset이 포함되어있다.

라이브러리 설치가 끝나면 루트 디렉토리에 node_modules라는 폴더가 하나 생기는 것을 볼 수 있다.
방금 설치한 패키지들이 들어있는 폴더이며, 일반적으로 파일 용량이 크기 때문에 repository에 올리지 않는 것이 원칙이다.

따라서, 루트 디렉토리에 .gitignore 파일을 하나 만들어준 다음 node_modules를 버전관리에서 제외시키도록 하자.

다음은 Babel 설정 작업이다.
Babel은 실행될 때 내부적으로 .babelrc라는 파일명을 찾아 설정 정보를 읽기 때문에 이 이름으로 파일을 하나 생성해줘야 한다.

> rc란 일반적으로 UNIX 운영체제에서 설정 파일을 의미하는 접미사

.babelrc 파일에 이번에 설치한 preset을 전달한 다음,
이제 빌드를 위한 폴더 구조를 잡아보도록 하자.

src 폴더를 하나 만든 후, 그 안에 app.js 파일을 만들어 그동안 작성했던 JSX 스크립트를 옮겨주도록 하자.

자, 여기까지 했으면 이제 빌드 스크립트를 작성해줄건데,
package.json 파일을 열어 "scripts"라는 객체를 만들어준 다음,
"src 폴더 내부에 있는 스크립트 파일들을 모두 트랜스파일하여 dist라는 폴더로 옮겨줘"
라는 의미로 다음과 같이 작성하자.
`"build": "babel src --out-dir dist"`

> babel: babel 실행 명령어, src: 원본 소스 폴더, --out-dir: 트랜스파일 결과물 저장하는 폴더 정의 옵션

자 그럼 npm run build 명령어를 한번 입력해보자.

실행이 완료되면 dist라는 폴더에 같은 이름으로 스크립트 파일이 하나 생성된 것을 확인할 수 있다.

```javascript
// dist/app.js
const App = () =>
  /*#__PURE__*/ React.createElement("h1", null, "Hello, React!");
ReactDOM.render(
  /*#__PURE__*/ React.createElement(App, null),
  document.getElementById("root")
);
```

파일을 열어보니, JSX 스크립트가 javascript로 변경되어있다.
이는 우리가 맨처음 작성했던 스크립트와 거의 동일한 것을 볼 수 있다.

자 그럼, 마지막으로 index.html 파일을 연 다음, dist 폴더에 스크립트를 실행하도록 수정해보자.
먼저 Babel CDN는 이제 필요없으니 주석처리를 해주자.
`<script src="dist/app.js">`
이런 식으로 스크립트를 변경한 다음, 파일을 실행해보면 에러가 말끔하게 사라진 것을 확인할 수 있다.

우리는 이번에 빌드 타임에서 스크립트를 트랜스파일하기 위한 많은 작업들을 진행해보았다.

1. local에서 Babel을 실행할 수 있도록 총 3개의 패키지를 설치했고,
2. 용량이 큰 파일들은 버전 관리에서 제외시켜주었다.
3. 그리고 Babel 실행을 위한 build 스크립트를 작성해보았다.

Babel의 Standalone 공식 문서를 확인해보면,
이런 트랜스파일 작업은 일반적으로 Webpack과 같은 번들러와 통합하여 운영한다고 적혀있다.

그럼 다음에는 번들러란 무엇인지, 그리고 왜 Babel을 번들러와 통합하여 운영해야하는지에 대해 알아보자.

## commit -m "with Webpack"

Babel의 공식 문서에 따르면, Babel은 이렇게 독립적으로 사용하지 않고,
보통 번들러와 통합하여 운영한다고 한다.

그럼 번들러란 무엇인지 알아보고, 간단한 번들링 설정을 진행해보도록 하자.

먼저, 번들러의 가장 본질적인 존재 이유,
번들링이란 무엇이며 왜 필요한지 먼저 살펴보자.

우리는 현재 app.js라는 파일 하나만 정의했다.
그런데, 보통 실제 개발할 때 이런 식으로 하나의 파일 안에 모든 로직을 몰아서 넣지는 않는다.
왜냐하면, 유지보수성이 많이 떨어지기 때문이다.
그런데 반대로, 어플리케이션이 실행되는 환경에서는 일반적으로 파일 수가 최소가 되는 것이 유리하다.

> 개발 단계 = 유지보수성을 위해 파일분리
> 운영 단계 = 파일 수를 최소화

여기서 "일반적으로" 라고 말하는 것은, 코드 스플리팅과도 관련이 있다.
이는 다음에 좀 더 자세히 다룰 것이고, 우선은 파일 수가 최소가 되는 게 좋다라고 이해하고 넘어가자.

왜 파일수가 최소가 되어야할까?
직접 코드를 작성해보면서 이해해보자.

자 지금부터 우리는 아주 간단한 계산기를 만들어볼것이다.
그러기 위해선, 덧셈, 뺄셈, 나눗셈, 곱셈을 위한 함수가 필요할 것이다.

이 함수들을 정의하기 위해, 먼저 src 폴더 하위에 utils라는 폴더를 하나 만들어주자.

유지보수성을 극단적으로 중요하게 생각한다는 가정 하에, 파일도 아주 극단적으로 나눠보자.
utils 폴더 하위에 덧셈, 뺄셈, 나눗셈, 곱셈을 위한 파일들을 생성했다.

이제 이 함수들을 app.js 파일 안에서 import 해주자.
그리고 이제, console로 각 결과도 찍어보자.
1+1, 1-1, 1\*1, 1/1의 결과를 찍어보고,

이제 npm run build 명령어를 터미널에 입력해보도록 하자.

```bash
$ npm run build

> build
> babel src --out-dir dist

Successfully compiled 5 files with Babel (192ms).
```

dist 폴더를 확인해보면, 우리가 원하는대로 트랜스파일이 잘 완료되었다.
근데, index.html 파일을 실행해보니, 에러가 발생하고 있다.
`Uncaught SyntaxError: Cannot use import statement outside a module`
이라는 에러는, 말 그대로 ES 모듈이 아닌 곳에서 import 라는 문법을 사용할 수 없다는 것이다.

이는 자바스크립트의 모듈 체계 특성으로 인해 app.js 안에서 사용한 import 문법에서 발생하는 에러이며,
script 태그에 `type="module"` 이라고 명시해주면 금방 해결할 수 있다.

> **자바스크립트 모듈 로드 문법은 왜 두개로 나눠졌을까?**
> 자바스크립트에서 모듈을 로드할 때 쓰는 방식은 알다시피 require와 import로 나눠져 있다.
> 어떤 환경은 require만 지원하고, 어떤 환경은 import만 지원하기 때문에 헷갈리는 개발자들이 많은데
> require와 import의 차이를 한 방에 이해해보도록 하자.
>
> 먼저, 둘의 차이를 이해하려면 javascript가 백엔드와 프론트엔드, 양쪽에서 모두 사용 가능하다는 점을 알아야 한다.
> 자바스크립트는 처음 등장한 1995년부터 2009년까지, 프론트엔드에서만 사용되었다.
> 이후 2009년, 자바스크립트로 백엔드를 개발할 수 있는 환경인 nodejs가 등장하였는데,
> 문제는 기존 자바스크립트 표준에 파일 시스템이나 네트워크를 관리할 수 있는 API가 존재하지 않았다는 것이다. 왜냐하면 이전에는 프론트엔드에서만 사용되었기 때문.
> 따라서, 백엔드 개발을 위한 새로운 표준이 필요했고, 여기서 등장한 것이 바로 **CommonJS**이다.
> CommonJS가 등장하면서 앞서 언급한 API 외에, 모듈 관리 체계가 함께 추가되었는데, 여기서 처음 등장한 모듈 로그 문법이 바로 **require**이다.
> 그래서, CommonJS가 적용되어 있지 않은 자바스크립트 환경에서 require를 사용하면 에러가 발생하는 것이다.
> 한편, CommonJS를 사용할 수 있는 환경은 백엔드 뿐이었기 때문에, 아직 프론트엔드에서는 이렇다할 모듈 관리 체계가 존재하지 않았다.
> 전역 변수나 script 태그를 이용하는 방법이 있었긴 하지만, 이런 방법들은 namespace의 충돌 문제와 성능적인 이슈가 있었다.
> 그래서 어떤 개발자들은 Webpack과 같은 번들러를 통해 CommonJS 환경을 흉내내어(적절한 모듈 관리 체계가 없으니, 일단은 우회적인 방법을 사용) 우회적인 방법으로 require을 사용하기도 했다.
> 즉, 중간 정리를 하자면, 백엔드와 프론트엔드의 자바스크립트 표준이 맞지 않다는 점, 그리고 프론트엔드에서 모듈 관리를 위한 적절한 체계가 없다는 점, 크게 두 가지 문제가 있는 상황이었다.
> 그러다 2015년, 자바스크립트의 표준을 통합하기 위해 대대적으로 개편한 표준이 바로 ES6인데, 이때 프론트엔드에서도 모듈을 관리할 수 있는 새로운 문법, **import**가 처음 등장한다.
> 그런데 여기서 한가지 의문이 생기는 것은, CommonJS에 이미 존재하는 require를 그대로 ES6 표준에 통합시키면 되는데, 왜 굳이 import라는 새로운 문법을 고안한 것일까?
> import가 require에 비해 나은 두 가징 강점이 있다. 바로 정적 분석과 코드 최적화에 유리하다는 점인데,
> 먼저, 코드 중간 아무데나 위치할 수 있는 require와는 달리, import는 코드 최상단에 위치해야 한다.
> **require(동적 구조 = 코드 아무데나 위치할 수 있음) / import(정적 구조 = 코드 최상단에 위치해야함)**
> 모듈 로딩 로직이 코드 중간에 위치한다면, 코드의 가독성이 크게 떨어지기 때문에, 이를 개선한 것이다.
> 물론, 프로젝트가 커질수록 가독성 저하 이슈는 더 커지기 마련이다.
> 또한, import는 의존적 모듈을 명시적으로 로드할 수 있어 해당 모듈에서 어떤 로직이 의존성에 해당하는지 알기가 쉽고, 트리 쉐이킹에도 유리하다.
> 여기서 트리 쉐이킹이란 빌드 타임에 사용되지않는 코드를 제거하여 번들 사이즈를 줄이는 최적화 기법을 말한다.
> 현재는 프론트엔드는 물론, nodejs의 LTS 버전에서도 ES6를 지원하기 때문에, 백엔드 환경, 프론트엔드 환경 모두 import의 사용이 가능하다.
> 만약 CommonJS만을 지원하는 구버전의 nodejs라면 require를 사용해야 하고, 프론트엔드 역시, 타겟 브라우저에 ES6가 지원되지 않는 구형 브라우저가 포함된다면 번들러를 사용하거나, 전역 변수를 이용하여 모듈을 관리해야 한다.
>
> 자, 이제 정리해보자.
> "자바스크립트는 백엔드 / 프론트엔드 모두에서 사용 가능한 언어"
> "2015년까지는 백엔드 쪽 자바스크립트 표준 (CommonJS)에만 모듈 관리 체계, require가 존재.
> 2015년에 ES6가 등장하면서, 프론트엔드에서도 모듈 관리가 가능한 import 문법이 등장.
> import는 파일 최상단에 위치해야 한다는 문법적 특성으로 인해, 정적 분석과 코드 최적화에 유리.
> 현재는 Nodejs의 LTS 버전에서도 ES6가 지원되므로, 백엔드 / 프론트엔드 모두에서 import 사용 가능.

자 다시 index.html을 구동시켜보자.
h1 태그가 잘 렌더링되고 있는 것을 보니, 에러가 잘 해결되었다.
계산 결과도 궁금한데, 개발자 도구를 한번 열어보자.

정확히 우리가 원했던 결과가 잘 찍히고 있다.
결과가 잘 나오고 있는데, 뭐가 문제일까?

네트워크 탭을 연 다음, 새로고침을 한번 해보자.
스크립트 파일들이 여러개 로드된 것을 볼 수 있다.

자, app.js 파일에서 import 했던 스크립트들이 모두 로드되고 있다.

### HTTP 요청

이게 왜 문제일까? 일단 스크립트가 로드되었다는 것은
HTTP 요청을 통해 서버로부터 원하는 스크립트를 응답받았다는 것이다.
즉, 만약 10개의 스크립트를 로드했다면, 10번의 HTTP 요청이 있었다는 것이다.

하나의 HTTP 요청에는 요청과 응답에 걸리는 시간(서버로 요청이 전송되는 시간 & 서버에서 응답을 처리하는 시간) 뿐 아니라, DNS 조회하는 시간, TCP 연결 설정 시간 등 부가적인 시간도 소요된다.

또한, 브라우저는 한 도메인 당 한 번에 요청할 수 있는 connection을 최대 6개로 제한하고 있다.
즉, 10개의 스크립트를 요청했다면, 먼저 요청된 6개 스크립트가 완전히 로드될 때까지, 나머지 4개는 waiting(pending) 상태에 걸리게 된다는 것이다.

따라서, HTTP 요청은 가급적 최소화해야한다.

그런데 지금 우리는 계산 결과 출력을 위해 불필요하게 많은 모듈들을 로드하고 있다.
**개발 과정에서는 유지보수성을 위해 모듈을 분리해 놓았지만, 어플리케이션이 구동되는 환경에서는 굳이 분리되어 있을 필요가 없다.**

**따라서, 스크립트 로드를 위한 HTTP 요청을 최소화하기 위해, 분리된 모듈을 다시 통합하는 과정이 필요하다.**

**이 과정을 번들링이라고 하며, Webpack, Rollup과 같은 번들러들이 담당하는 주요 기능이다.**

자 그럼 번들러가 어떤 역할을 하는지 알아보았으니, Webpack 설정을 간단하게 해보도록 하자.

먼저, build time에서 Webpack을 실행시키기 위한 패키지 2개를 설치해주자.

```bash
$ npm install --save-dev webpack webpack-cli
```

그 다음, Babel과 통합하기 위한 babel-loader도 필요한데, 이것도 같이 설치하도록 하자.

```bash
$ npm install --save-dev babel-loader
```

Babel 설정을 위해 `.babelrc` 파일을 정의했듯이, Webpack도 마찬가지로 설정 파일이 존재한다.
바로 webpack.config.js라는 파일인데, 이 이름으로 파일을 하나 만들어주자.

webpack은 build 환경인 Node.js 환경에서 실행되기 때문에, CommonJS 문법인 module.exports 문법을 사용하여 지금부터 작성할 객체를 export 해보자.

먼저 entry란, 번들링의 시작점을 지정해주는 옵션이다.
우리는 계산 결과 출력을 위한 util 함수들을 정의해놓고, app.js에서 import 하기 때문에, 이 파일을 번들링 시작점으로 지정해주면 되겠다.

그 다음 번들링 결과물을 어느 폴더에 저장할지, 그리고 어떤 파일명으로 저장할지에 대해 output 옵션에 지정해주면 된다.

마지막으로 module 옵션을 지정해주면 된다.
module이라는 것은 다양한 파일 형식(파일 확장자)을 어떻게 처리할지 지정하는 옵션이며,
우리가 웹 어플리케이션에서 다루는 파일 확장자는 아주 다양하다.
js, jsx와 같은 스크립트 파일도 있고, ts, tsx와 같은 타입스크립트 파일도 있다.
또한, css, scss와 같은 스타일시트 파일들도 있다.

그런데 Webpack은 기본적으로 번들링 과정에서 javascript 코드만 처리할 수 있기 때문에,
이 외 파일들은 모두 번들링하기 전에 **전처리**가 필요하다.
여기서 전처리에 사용되는 모듈이 바로 **loader**이다.

우리도 마찬가지로, app.js 파일 안에서 vanilla javascript가 아닌, JSX 문법을 사용하고 있기 때문에,
babel-loader를 통한 전처리 과정이 필요하다.
rules라는 배열을 하나 정의해주고, 여기에 어떤 확장자를 babel-loader로 처리할건지, 정규식을 활용하여 표현해주면 된다.
단, node_modules같은 경우에는, 다른 사람들이 배포한 패키지가 담긴 폴더이고, 이것들은 이미 실행 가능한 형태로 build되어 있기 때문에, exclude 옵션을 통해 제외시켜준다.
use 옵션을 통해 js 파일에 babel-loader를 사용하도록 설정해주면 이것으로 간단한 Webpack 설정은 끝난다.

그럼 package.json 파일을 열어, build 명령어를 webpack으로 변경한다음,
npm run build 명령어를 입력해보면 dist 폴더 안에 bundle.js라는 이름의 파일이 하나 생성된것을 볼 수 있다.

index.html 파일을 열어서, 이 bundle.js 파일을 실행하도록 스크립트 태그를 수정한 다음,
파일을 한번 실행해보자.

**네트워크 탭을 열어보면, 스크립트 파일 로드가 많이 줄어든 것을 확인할 수 있다.**

마지막으로, 보통 이런 build 폴더도 버전 관리하지 않는것이 원칙이기 때문에,
한번 제거해 준다음, .gitignore에 추가해주도록하자.

그리고 다시 npm run build 명령어를 입력해보자.
그런데, npm run build 명령어를 실행하니, WARNING이 하나 발생하는 것을 볼 수 있다.

```bash
The 'mode' option has not been set, webpack will fallback to 'production' for this value.
Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/
```

이 경고는 번들러 실행 모드를 따로 설정해주지 않았다는 것인데,
그럼 이제 모드 설정을 비롯한 추가적인 Webpack 설정을 진행해보도록 하자.

## commit -m "with Webpack Plugins"

우리는, 번들링의 개념이 무엇인지 알아본 다음, 기본적인 Webpack 설정을 진행했다.
그런데, build 명령어를 실행하니, 다음과 같은 에러가 발생하고 있었다.

```bash
The 'mode' option has not been set, webpack will fallback to 'production' for this value.
Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/
```

Webpack 실행 모드를 따로 설정해주지 않았다는 것인데,
왜 이런 에러가 발생했는지 알아보고, 개발 환경을 좀 더 편리하게 만들어 줄 Webpack 설정을 진행해보도록 하자.

Webpack의 모드 속성은 production과 development, 두 가지 값을 가질 수 있다.
일반적으로 운영 단계에서는 production, 개발 단계에서는 development 옵션을 사용한다.
두 옵션의 차이는 크게 빌드 속도와 코드 최적화 여부에 있는데(코드 최적화 과정이 있냐 없냐에 따른 빌드 속도 차이 발생),
두 옵션으로 모두 빌드를 진행해본 다음 결과를 비교해보자.

Webpack 실행이 완료외면 빌드 타임이 얼마나 걸렸는지 확인할 수 있는데,
production 모드가 시간이 더 오래 걸린 것을 볼 수 있다.

bundle.js 파일을 열어보면 왜 이런 결과가 발생했는지 알 수 있는데,
먼저 development 모드로 빌드한 결과물에는 우리가 사용했던 addNumbers 같은 함수명이 코드 상에 그대로 남아있다.
반면, production 모드로 빌드한 결과물에는 addNumbers라는 함수명을 찾을 수 없다.

이렇게 코드가 변경된 이유는 production 모드에서 상수 폴딩이라는 최적화 기법이 자동으로 적용됐기 때문이다.

이처럼, production 모드에서는 코드 최적화 과정이 자동으로 포함되기 때문에,
상대적으로 빌드 속도가 느린 것이다.

> production : 코드 최적화(트리 쉐이킹, 코드 스플리팅, 코드 난독화 등) 과정이 자동으로 포함됨
> development : 코드 최적화 과정이 자동으로 포함되지 않음

이 최적화 과정에는 트리 쉐이킹, 코드 스플리팅, 코드 난독화 등의 작업도 포함된다.

실행 속도보다 빌드 속도가 더 중요한 개발 단계인 development 모드에서는 이 과정을 의도적으로 생략하기 때문에
빌드 속도가 빠른 것이다.

현재 우리는 개발 환경이기 때문에, 일단은 mode를 development로 설정해주도록 하자.

자, 지금부터는 플러그인이라는 것을 설치해줄 것인데,
그 전에, 지금까지 우리는 어떤 도구를 적용하던 간에, 먼저 "왜 설치애햐 하는지"를 이해한 다음 적용했었다.
왜냐하면 특정 도구를 적용하지 않으면 아예 동작하지 않거나, "경고" 메시지가 나왔기 때문이다.
즉, 도구를 적용해야하는 이유가 "명확"했다.

하지만 지금부터 설치할 플러그인은 이런 식으로 적용해야할 이유를 친절하게 알려주진 않는다.
그 이유는 우리가 직접 느껴야 한다는 뜻인데, 특히 이런 빌드 도구를 다루는 경우, 프로그래밍적인 불편함을 느끼는 것이 중요하다.

그럼 지금부터 의도적으로 불편함을 한번 만들어보자.
일단, dist 폴더를 한번 제거하고 webpack config 파일을 열어, output filename 값을 수정해보도록 한다.

Webpack에서는 파일명에 [hash]라는 값을 붙이면, 번들 파일에 해쉬값이 자동으로 붙는데,
output filename 값을 "bundle.[hash].js" 으로 수정해본 다음 npm run build 명령어를 입력해보자.
그러면 dist 폴더 내에 실제로 해시값이 붙은 bundle.c8beb8b2babd2e4d9e5e.js 파일이 생성된 것을 볼 수 있다.
bundle. 다음에 붙은 해시값은 실제로 빌드 명령어를 수행할 때마다 변경되는데,
그럼 우리는 index.html에 script 태그에서 매번 bundle.js 파일명을 해시값이 붙어있는 파일명으로 수정해줘야할까?
또한 해시값은 붙은 번들 파일과 index.html에서 불러오는 파일명이 달라진다면, 당연히 정상적으로 동작도 하지 않게 된다.

**이런 식으로 빌드할 때마다 스크립트 태그를 수정해주는 것은 굉장히 번거로운 일이 아닐 수 없다.**
심지어, real world 프로젝트에서는 이 번들 파일이 여러 개인 경우가 많다.
상상만 해도 굉장히 불편할 것 같다.

자, 이 불편함을 해결해 줄 플러그인이 바로 `html-webpack-plugin` 이다.
아래 명령어를 입력하여 패키지를 설치하자.

```bash
npm install --save-dev html-webpack-plugin
```

이제 webpack.config.js 파일에 plugins라는 이름의 배열을 정의하고, 방금 설치한 플러그인을 import하자.
패키지를 import 한 다음, 마우스를 갖다 대면, 패키지가 class 문법으로 작성되어있는 것을 볼 수 있다.
따라서, new 키워드로 인스턴스를 하나 생성한 다음, plugins 배열 안에 전달하면 된다.
플러그인에 두 parameter를 전달해주도록 할건데,
먼저, template 옵션은 기준이 되는 Html 파일명을 지정해주면 된다.
우리는 root path에 있는 index.html 파일에 script 태그를 삽입할 것이기 때문에, 이 경로를 지정해주자.
output filename도 동일하게 index.html로 지정해주자.

자, 이 상태에서 npm run build 명령어를 입력해보면 dist 폴더 안에 index.html 파일이 하나 생성되고,
bundle 파일 경로도 잘 설정된 것을 볼 수 있다.

마찬가지로 dist내의 index.html 파일을 실행해보면 h1 태그가 잘 렌더링 되는 것을 확인할 수 있다.

지금까지 살펴본 plugin을 한 문장으로 정리하면,
**빌드 프로세스의 특정 시점에 후킹되어, 추가 기능을 수행하는 확장 모듈**이라고 할 수 있다.

즉, 빌드 환경을 구성하는 과정에서 뭔가 불편한 점이 느껴진다면, 앞으로는 적절한 plugin을 찾아,
이 plugins 배열에 전달해주면 되겠다.

유용한 플러그인들은 [Awesome Webpack](https://webpack.js.org/awesome-webpack/) 사이트의 Webpack Plugins라는 섹션에서 어느 정도 찾아볼 수 있다.

쭉 보다보면 재미있는 플러그인들이 많으니, 시간되면 한번씩 참고해보는 것을 추천한다.
예를 들면, Service Worker를 사용하는 프로젝트에서 관련 파일을 자동으로 설정해주는 플러그인도 있고, 번들링 결과 분석 도구를 자동으로 만들어주는 플러그인도 있다.
또, 특정 파일을 다른 폴더에 복사해주는 플러그인도 있다.

만약 찾는 플러그인이 없다면, 직접 플러그인을 정의하는 방법도 있으며 이는 [Plugins API](https://webpack.js.org/api/plugins/)를 참고하면 되겠다.

자, 정리해보자.
우리는 플러그인이란 무엇인지, 어떤 역할을 하는지에 대해 알아보았다.
다음에는 dev tool에 대해 알아볼 것인데,
빌드 과정에서 번들링 결과물에 대한 추가 작업을 자동화해주는 플러그인과는 달리,
dev tool은 개발 환경 자체에 추가 기능을 제공하기 위한 툴들이다.
