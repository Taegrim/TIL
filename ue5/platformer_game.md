# 블루프린트를 이용한 플랫포머 게임
1. 플레이어 인풋 제어
2. 점프대 발판
3. 움직이는 발판
4. 플레이어를 튕겨내는 피스톤 블럭
5. 트리거 함정 
#
<br><br><br>


+ ### Enhanced Input System을 이용한 플레이어 인풋 제어
````
Input Action : 무엇을 할 것인가
Input Mapping Context : 어떤키로 IA를 작동할 것인가
Trigger & Modifier : 입력값의 조절(데드존, 반전)
````
<br><br>

<img width="1120" height="680" alt="IA_MOVE" src="https://github.com/user-attachments/assets/7a8366d8-f98a-4255-bf58-b6f513e1cc66" />
<br>
Move, Look 의 경우 Vector2D<br><br>

<img width="971" height="638" alt="IA_JUMP" src="https://github.com/user-attachments/assets/0cc95875-aa14-4d14-b634-a496b247d46e" />
<br>
Jump의 경우엔 눌렸는지만 판단하므로 bool
<br><br>

<img width="1760" height="1209" alt="IMC_DEFAULT" src="https://github.com/user-attachments/assets/9d3b9a4d-04c8-4ce4-883d-d62c6501823e" />
<br>
IA의 동작을 IMC에서 특정 키에 매핑한다<br>
현재 맵에선 앞, 오른쪽이 양수방향이므로 뒤, 왼쪽에는 Negate로 값을 반전해야한다.
<br><br>

<img width="997" height="837" alt="IA 동작 구현" src="https://github.com/user-attachments/assets/597e5b28-1660-4357-9831-0b02bd4c045c" />
<br>
게임이 시작할 때 매핑한 IMC를 플레이어가 사용하도록 한다
IA에 대한 동작은 노드로도 간단하게 구현할 수 있다.
<br><br>


### Enhanced Input System의 장점
걷기용 IMC, 자동차용 IMC를 분리하고 동작을 따로 정의해서 플레이어 조작을 쉽게 교체할 수 있다
<br><br><br><br>



+ ### 점프대 발판
![점프대 발판 gif](https://github.com/user-attachments/assets/1aa44e07-a674-43b0-b31b-7ed46fe55a2e)
<br><br>
<img width="889" height="349" alt="점프대 발판 노드" src="https://github.com/user-attachments/assets/30694745-61b8-4077-90da-178408faf533" />
<br>
발판 위의 충돌체와 Overlap되면 플레이어인지 확인 후 LaunchPlayer로 플레이어를 위로 발사한다.
<br><br><br>


----
### Add Impulse와 LaunchPlayer의 차이

+ Add Impulse
    + 충격량에 기반한 물리
    + 질량에 영향받음
    + 보다 세밀하게 제어 가능
 + Launch Player
    + 캐릭터를 Falling 상태로 변경
    + 캐릭터의 속도에 관계없이 정해진 힘으로 날리기 가능
    + 캐릭터의 질량 무시
----

<br><Br><br><br>


+ ### 움직이는 발판
![움직이는 발판 gif](https://github.com/user-attachments/assets/eb67d387-6cbe-4aab-a7f2-9cc6bd613678)
<br><br>
<img width="1094" height="471" alt="움직이는 발판 노드" src="https://github.com/user-attachments/assets/30f6336b-dac0-4538-8fae-7c706c76a928" />
<br>
Timeline, Lerp, FlipFlop 으로 발판을 왕복하게 했다
<br><br>


<img width="1110" height="525" alt="Timeline" src="https://github.com/user-attachments/assets/f1bb72c8-0747-4277-afbf-3df7f59a8526" />
<br>
Key InterPolation 을 Auto로 두어서 양 끝에서는 천천히 움직이도록 했다<br>
InterpToMovement 를 사용하는것이 간단하지만 양 끝에서 감속이 되도록 하기 위해 TimeLine을 사용하여 구현했다.
<br><Br><br><br>





+ ### 피스톤 블럭
![피스톤 블럭 gif](https://github.com/user-attachments/assets/e6b4f5cf-7e17-4969-bba2-fd551ae69d97)
<br><br>
<img width="917" height="619" alt="피스톤 블럭 노드" src="https://github.com/user-attachments/assets/661efb92-9b2a-4da3-bb2e-d27f99e2734c" />
<br>
앞에서 움직이는 발판을 구현했던것과 똑같이 피스톤 부분을 왕복시켜준다<br>
피스톤 끝부분과 플레이어가 충돌 시 점프대에서 플레이어를 위로 튕겨낸것처럼 피스톤의 앞 방향으로 플레이어를 튕겨낸다.
<br><br><br><br>




+ ### 트리거 함정
![트리거 함정 gif](https://github.com/user-attachments/assets/6aee1611-840b-40ed-bc37-a0c60047992e)
<br><br>
<img width="1063" height="526" alt="트리거 함정 배치" src="https://github.com/user-attachments/assets/a63d5186-fa33-4379-80f2-aec3c66ff305" />
<br>
플레이어가 트리거를 작동하면 경사로를 따라 함정이 굴러오도록 배치했다
<br><br>

<img width="1407" height="303" alt="트리거 노드" src="https://github.com/user-attachments/assets/df4312ed-9842-48fe-abf2-cbc65c24f1e8" />
<br>
트리거가 어떤 액터를 조종할지 알려주는것이 필요하다<br>
변수로 BP_Rock을 참조하는 Target 변수를 만들고 Instance Editable로 설정했다<br>
이러면 뷰포트에서 어느 객체를 참조할 지 선택할 수 있다
<br><br>


<img width="918" height="412" alt="Begin, End Overlap" src="https://github.com/user-attachments/assets/6b29d503-3a27-4ced-88a9-cd9423c943f2" />
<br>
구체와 플레이어가 충돌중인지를 Begin, End Overlap으로 판단했다
<br><br>

<img width="989" height="241" alt="Tick Event로 플레이어 밀어내기" src="https://github.com/user-attachments/assets/965521a5-32e5-48b4-b86e-d85f6dea06f9" />
<br>
구체는 플레이어와 충돌중이라면 이동하는 방향으로 플레이어를 밀어낸다
<br><br><br><br>

+ ### 트리거 함정 구현 중 발생한 문제점
<br>

````
Overlap 에만 플레이어를 밀어내니 플레이어를 지나치는 문제가 있었다
그리고 플레이어가 오히려 공을 밀어버리는 문제가 있었다

기존에 Overlap시에만 충돌처리 하던걸 Overlap은 충돌 중인 상태만 변경하고
Tick Event에서 지속적인 밀어내기를 처리하니 지나치는 문제가 사라졌다

플레이어의 Enable Physics Interaction 을 체크 해제하고
Rock의 스태틱 메시의 Collision에서 Pawn과의 충돌만 Ignore로 변경하니 플레이어가 돌을 밀지 못하게 되었다
````
