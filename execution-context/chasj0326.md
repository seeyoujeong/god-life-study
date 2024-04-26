## 실행 컨텍스트

실행할 코드에 제공할 환경 정보들을 모아놓은 객체

크게는 Variable Environment 와 Lexical Environment 정보를 담고 있다.

### **VariableEnvironment**

현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보

**선언 시점의 LexicalEnvironment의 스냅샷**

### **LexicalEnvironment**

처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영된다.

> **environmentRecord (환경 레코드)**
> 
- **현재 컨텍스트와 관련된 코드의 식별자 정보**
- 함수, 변수, this 등 식별자 바인딩 (declarative)

> **outerEnvironmentReference (외부 환경 참조)**
> 
- 상위 스코프 정보
