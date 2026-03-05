# 가변 인자 템플릿
+ 템플릿을 사용해서 임의 개수의 인자를 가질 수 있음
+ 가변 인자 템플릿을 이용해서 임의의 인자를 받는 생성자를 구현
#
<br>


+ ### 가변 인자 템플릿의 모습
````
template <typename T>
void PrintAll(T arg) {
    std::cout << arg << std::endl;
}

template <typename T, typename... Args>
void PrintAll(T arg, Args... args) {
    std::cout << arg << ", "; 
    PrintAll(args...);         
}

int main()
{
    PrintAll(1, 2.3, "msg");
}
````
````
template <typename T, typename... Args>
````
Typename뒤의 ...을 템플릿 파라미터 팩 이라고 한다<br>
템플릿 파라미터 팩은 0개 이상의 템플릿 인자를 나타낸다
````
void PrintAll(T arg, Args... args) {
````
함수의 인자에 ...는 함수 파라미터 팩 이라고 부르며 0개 이상의 함수 인자를 나타낸다<br>
````
 PrintAll(1, 2.3, "msg");
````
위의 함수를 호출하면 
````
void PrintAll(int arg, double arg2, const char* arg3) {
    std::cout << arg << ", "; 
    PrintAll(arg2, arg3);         
}
````
와 같은 형태로 호출된다
<br><br>

+ ### C++17 fold 표현식
````
template<typename... Args>
void PrintSum(Args... args) {
    auto sum = (args + ...);
    // auto sum = (... + args);
    std::cout << "합계: " << sum << std::endl;
}

template<typename... Args>
void PrintAll(Args... args) {
    (std::cout << ... << args) << std::endl;
}

int main()
{
    PrintSum(1, 5, 2.4);
    PrintAll(1, "msg", 3.5);
}
````
앞에서 재귀함수 형태로 작성해야 했던것을 폴드 표현식을 사용하면 간단하게 표현할 수 있다<br>
Fold 방식은 아래와 같다<br><br>

|이름|Fold 방식| 전개 형태|
|---|---|---|
|(E op ...)|단항 우측|$(E_1\; op (...\; op (E_{N-1}\; op\; E_N)))$|
|(... op E)|단흥 좌측|$(((E_1\; op\; E_2)\; op\;...) op\; E_N)$|
|(E op ... op I)|이항 우측|$(E_1\; op\; (...\; op\; (E_{N-1}\; op\; (E_N\; op\; I))))$|
|(I op ... op E)|이항 좌측|$((((I\; op\; E_1)\; op\; E_2)\; op\; ...)\; op\; E_N)$|

<br><br><br>

가변 인자 템플릿을 실제 코드에서 아래와 같이 사용했다
````
template <typename... Args>
	Status(Args... args) {
		// 가변인자 개수 체크
		static_assert(sizeof...(args) <= STATUS_COUNT,
			"인자가 STATUS_COUNT 를 초과했습니다.");

		// 타입 체크
		static_assert((std::is_convertible_v<Args, int> && ...),
			"인자는 int 타입으로 변환 가능해야합니다");

		level = 1;

		// 가변인자를 이용한 status 채우기
		/*int i = 0;
		status.resize(STATUS_COUNT, 0);
		((status[i++] = static_cast<int>(args)), ...);*/

		// c++ 스타일 이니셜라이저 리스트
		status = { static_cast<int>(args)... };
		status.resize(STATUS_COUNT, 0);
	}
````
Status 라는 클래스는 현재는 6개의 스탯을 가지고 있지만 기획에 따라 변할 수 있다<br>
또한 일부 스탯까지만 사용하고 넘길수도 있다<br>
따라서 가변 인자 템플릿을 이용해서 클래스의 생성자를 구현해봤다<br><br>

````
static_assert(sizeof...(args) <= STATUS_COUNT,"{에러메세지}");
````
위의 코드는 가변 인자 개수가 스탯 개수를 초과할경우에 컴파일 에러를 발생시킨다
````
static_assert((std::is_convertible_v<Args, int> && ...),
			"{에러메세지}");
````
위의 코드는 문자열과 같이 int로 변환이 불가능한 타입이 인자로 올 경우 컴파일 에러를 발생시킨다<br>
````
status = { static_cast<int>(args)... };
status.resize(STATUS_COUNT, 0);
````
인자로 받은 args로 벡터를 초기화한다<br>
이후 값들은 0으로 초기화한다
<br><br><br>


## 주의사항
템플릿은 호출 시점에 코드가 생성되어야 하므로 클래스를 작성할 때 .cpp가 아닌 .h에서 작성해야 "외부 참조 오류"를 피할 수 있었다.

