# 클래스 템플릿
+ 템플릿을 이용한 다양한 타입을 받는 클래스
+ 간단한 벡터 구현
#
<br>


+ ### std::vector
std::vector 는 다음과 같이 구성되어있다<br>
1. T* data : Heap 영역에 동적할당된 연속된 메모리 배열을 가리키는 포인터
2. size : 현재 vector에 들어있는 원소 개수
3. capacity : 현재 할당된 메모리가 수용할 수 있는 최대 원소 개수(수용량)
<br>

원소를 추가하다 capacity를 넘을 경우 기존 capacity를 1.5배 혹은 2.0배 만큼 늘리고 <br>
heap 영역에 새로 메모리를 할당한 뒤 기존 메모리를 복사(이동) 한다<br>
메모리의 복사(이동)이 끝나면 기존에 할당했던 메모리를 해제한다
<br><br>

+ ### vector와 유사하게 작동하는 Inventory 클래스
<img width="881" height="734" alt="Inventory" src="https://github.com/user-attachments/assets/cbd175a9-aa3c-4955-b676-5358421cd458" />
<br>

벡터와 유사하게 작동하는 Inventory 클래스를 구현했다<br>
클래스 템플릿이므로 헤더 파일에 구현부를 통합했다<br>

```
void SortItems() {
		std::sort(items, items + size, [](const T& a, const T& b) {
			return a.GetPrice() < b.GetPrice();
			});
	}
```
기존에 작성했던 코드인데 이 코드는 템플릿 T 가 GetPrice를 무조건 가지고 있을것이라 예상한 함수이다<br>
내가 작성한 Item 클래스는 GetPrice를 가지고 있어 문제가 없지만 int타입은 GetPrice가 없어 해당 함수를 거치면 에러를 발생한다<br>
이는 다양한 타입을 받을 것이라는 템플릿 작성법에 위배되는 부분이다<br><br>
따라서 아래와 같이 코드를 수정했다

```
void SortItems() {
	std::sort(items, items + size);
}	
```
```
bool Item::operator<(const Item& other) const
{
	return this->price < other.price;
}
```
기본 sort의 경우 오름차순으로 < 비교 연산을 수행한다<br>
일반적인 변수 타입의 경우 operator< 가 기본적으로 있어서 문제가 없고 내가 만든 클래스의 경우에만 연산자 오버로딩으로 operator<를 추가해서 문제를 해결했다<br>

