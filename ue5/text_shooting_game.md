# 블루프린트를 이용한 텍스트 게임
1. 무기 초기화 및 재장전
2. 사격 및 과열 처리
3. 총기 냉각 처리
4. 무기 교체

----
### 추가구현
1. 자동 냉각
----
<br><br>

+ ### 무기 초기화 및 재장전
<img width="1135" height="402" alt="초기화" src="https://github.com/user-attachments/assets/b4bfc9a0-6046-45ec-b39d-2ccf803817a7" />
<br>
현재 Weapon 구조체는 Weapons 배열에 담겨있다.<br>
총기가 추가되어도 쉽게 변경할 수 있도록 WeaponName을 Enum으로 관리한다.<br><br>

재장전은 R키를 눌렀을 때도 사용하므로 함수로 처리했는데<br>
일반적인 ForEachLoop로 구조체를 가져오면 구조체의 반환값이<br>
레퍼런스가 아닌 복사본이므로 값이 적용되지 않는 문제가 있어<br>
Enum을 기준으로 Loop를 돌도록 하고 Index로 배열에서 ref를 가져오게 했다.

<img width="1025" height="513" alt="재장전 함수" src="https://github.com/user-attachments/assets/b30e5ae8-6fba-4b68-bdd8-60b89f358681" />
<br>
재장전 함수는 Weapon의 레퍼런스를 받아서 해당 무기의 최대 총알값으로<br>
현재 총알에 넣어준다.<br><br>

<img width="1363" height="493" alt="Image" src="https://github.com/user-attachments/assets/973ad543-d7f8-4932-8ea3-6bfc6f2b48d7" />
<br>
이후에 R키를 누르면 재장전 처리할 때도 같은 함수를 사용했다.<br>
앞에서 초기화할 땐 loop를 돌기에 enum 인덱스를 넣어주지만 수동 재장전은 현재 무기를 기준으로 장전하는 차이가 있다.<br><br>


+ ### 사격 및 과열 처리
<img width="1535" height="464" alt="사격 처리" src="https://github.com/user-attachments/assets/8e61e1b4-dce2-4494-8b7d-c5929adb38e4" />
<br>
<img width="1591" height="418" alt="게터" src="https://github.com/user-attachments/assets/43b58470-6f59-4a0d-adfe-3c3ac6594110" />
<br>
<img width="1676" height="780" alt="과열 처리" src="https://github.com/user-attachments/assets/f9fef03f-d70e-4aec-9d00-8f22519ee123" />
<br>
배열에서 구조체를 가져올땐 항상 Get(ref)로 신경썼다.<br>
복사본을 가져올 경우 메모리 낭비가 있을 수 있고 원본에 적용이 안되므로 ref를 이용했다.<br><br>

SetTemperature 함수에서는 지역변수를 사용했다.<br>
기존에 Break 노드에서 구조체의 변수를 가져와서 사용했더니 한눈에 알아보기가 어려웠기 때문이다.<br><br>
그리고 자료에선 branch의 결과마다 구조체에 set을 하는 방식으로 했지만<br>
나는 지역변수에 상태만을 저장하고 branch 분기가 완료되면<br>
한번만 Set members in Weapon 노드를 사용하도록 변경했다.<br>
개인적으로 이 방법이 가독성이 좋다고 생각해서 이렇게 처리해봤다<br><br>


+ ### 총기 냉각 처리
<img width="1288" height="550" alt="총기 냉각 처리" src="https://github.com/user-attachments/assets/7784f8be-730a-4bd3-8cc8-9fe7efc62669" />
<br>
총기 냉각의 경우에 Get 노드의 Split 옵션을 사용했다.<br><br>

Split 옵션의 사용과 Break 노드의 사용 기준은 개인적으로 아래와 같다.<br>
* Split - 구조체의 변수를 읽기만 할 때(노드에 구조체 반환이 없음)<br>
* Get(ref) + Break - 값을 가져오고 해당 구조체의 값을 바꿀 때<br><br>


+ ### 무기 교체
<img width="468" height="542" alt="무기 교체" src="https://github.com/user-attachments/assets/fbb717ac-39d1-43f7-9ac8-d8e3dd728386" />
<br>
<img width="1648" height="601" alt="무기 교체 함수" src="https://github.com/user-attachments/assets/4e0672e6-d2ad-426b-bd98-0ad130b345db" />
<br>
무기 교체 또한 Index를 Input으로 받는 함수로 처리했다.<br>
구분이 필요한 출력은 Format을 이용해서 현재 무기의 이름을 같이 출력한다.<br><br><br>

----
+ ### 결과 화면
<img width="1238" height="701" alt="과제 결과" src="https://github.com/user-attachments/assets/0b5597b9-e33b-4347-a448-2d6ab474c93d" />
<br>

----
<br><br><br>


+ ### 추가 구현 - 자동 냉각 처리
<img width="1490" height="362" alt="자동냉각" src="https://github.com/user-attachments/assets/265ab922-8ce5-48aa-84f4-e8b005fc1109" />
<br>
이전에 사격 처리 마지막에 현재 총기가 마지막으로 발사한 시간을 기록했다.<br>
그리고 Event Tick 에서 이를 검사해서 발사한지 3초가 지났다면 자동으로 Cooldown이 되도록 한다.<br>
3초마다 1씩 냉각되도록 하였는데 발사를 기록한 변수를 재사용해서 냉각처리를 했다면 다시 3초를 기다리도록 했다.<br><br>

앞서 냉각 처리를 할때 항상 현재 무기를 기준으로 처리해서 해당 함수를 재사용하여 자동 냉각처리를 하니 무기를 교체해도 다른 무기가 자동 냉각이 되지 않는 문제가 있었다.<br><br>
그래서 함수 Input으로 index를 추가로 받도록 바꾸고 관련된 부분을 고치니 무기를 발사하고 과열된 상태라면 3초에 1씩 자동으로 냉각되도록 구현할 수 있었다.<br><br>

----
+ ### 결과 화면
<img width="1243" height="691" alt="자동 냉각 화면" src="https://github.com/user-attachments/assets/52ffe2ca-0425-4a38-9059-d1085376b656" />
<br>

----
<br><br>