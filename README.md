# 코틀린 타입 시스템

코틀린의 타입 시스템은 코드의 가독성을 향상시키는 데 도움이 되는 몇 가지 특성을 새로 제공한다.   
이러한 특성으로는 널이 될 수 있는 타입과 읽기 전용 컬렉션이 있다. 또한 코틀린은 자바 타입 시스템에서 불필요하거나 문제가 되던 부분을 제거했다. 배열 지원이 그런 예에 속한다.

# 널 가능성

널 가능성이라 함은 NullPointerExcep 오류를 피하기 위해 생긴 코틀린 타입 시스템의 특성으로, 프로그램 사용자로써 자세한 추가정보가 없는 "An error has occured: java.lang.NullPointerExcep ..." 또는 "Unfortunately, the application X has Stopped" 같은 메시지를 본적이 있을 것이다. 이런 오류는 아주 치명적인 오류인데 코틀린을 비롯한 최신 언어에서 null에 대한 접근 방법은 가능한 한 이 문제를 실행 시점에서 컴파일 시점으로 옮기는 것이다.   
널이 될 수 있는지 없는지에 대한 여부를 타입 시스템에 추가함으로 발생할 수 있는 예외상황을 줄여줄 수 있다.

# 널이 될 수 있는 타입

널이 될 수 있는 타입은 프로그램 안의 프로퍼티나 변수에 null을 허용하게 만드는 방법이다.   
어떤 변수가 널이 될 수 있다면 그 변수를 수신객체로서 메소드를 호출하면 NullPointer가 발생할 수 있으므로 안전하지 않다. 그럼 널이 될 수 있는 타입의 동작을 배워보기 위해 자바코드를 먼저 보자.
<pre>
<code>
int strLen(String s){
  return s.length();
}
</code>
</pre>

이 함수에 null이 들어가게 되면 무조건 NullPointer가 발생한다. 함수에 대해 s가 null인지에 대한 검사는 이 함수를 사용하는 의도에 따라 달라진다.   
그렇다면 코틀린으로 다시 보자. 코틀린에서 이런 함수를 작성할 때 가장 먼저 답을 알아야 할 질문은 이 함수가 과연 널을 인자로 받을 수 있는가이다.    
만약에 널이 인자로 들어올 수 없다면 코틀린에서는 다음과 같이 함수를 정의한다.
<pre>
<code>
fun strLen(s: String) = s.length
</code>
</pre>
s에 널인 인자를 넘기는 것이 금지되며 혹시 넘기더라도 컴파일 할 때 애초에 막혀버린다.
strLen함수에서 파라미터 s의 타입은 String인데 코틀린에서 이 표현은 s는 항상 무조건 String의 인스턴스여야 한다는 뜻이다. 그러므로 컴파일러가 널이되는 값을 거부하는 것이다.   
이런 타입을 가지게 되면 s의 값이 널이 아니라는것에 대한 보증이 된다.   

만약 함수가 널과 문자열 모두를 받는다 하면 타입 뒤에 물음표를 붙인다.
<pre>
<code>
fun strLenSafe(s: String) = ...
</code>
</pre>

하지만 알아둬야 할것은 널이 될 수 있는 타입이라면 그에 대해 수행할 수 있는 연산도 제한된다.   
예를 들어 널이 될 수 있는 타입인 변수에 대해 변수.메소드() 형태 처럼 메소드를 직접 호출할 수 없다.

<pre>
<code>
fun strLenSafe(s:String?) = s.length()
ERROR: only safe...
</code>
</pre>

또는 널이 될 수 있는 값을 널이 될 수 없는 타입의 변수에 대입할 수 없다.

<pre>
<code>
val x: String? = null
val y: String = x
ERROR: Type mismatch...
</code>
</pre>

또는 널이 될 수 있는 타입의 값을 널이 될 수 없는 타입의 파라미터를 받는 함수에 전달할 수 없다.

<pre>
<code>
strLen(x)
ERROR: Type mismatch...
</code>
</pre>

그럼 도대체 널을 허용하는 타입의 값은 어디에 쓰일까?   
가장 중요한 일은 바로 null과 비교하는 것이다. 일단 null과 비교하고 나면 컴파일러는 그 사실을 기억하고 null이 아님이 확실한 영역에서는 해당 값을 널이 될 수 없는 타입의 값처럼 사용할 수 있다.

<pre>
<code>
fun strLenSafe(s: String?): Int =
  if(s != null) s.length else 0
</code>
</pre>
