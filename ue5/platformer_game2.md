# 블루프린트를 이용한 플랫포머 게임
1. ABP, 상태머신, 블렌드 스페이스
2. 빙판 플랫폼으로 마찰 제어, Overlap 처리
3. 사라지는 플랫폼, Dynamic Material Instance
4. 엘리베이터 플랫폼, Custom Event
#
<br><br><br>


+ ### 애니메이션 처리
````
Animation BluePrint, Blend Space, 상태머신을 이용한 애니메이션 처리
````
<br>

<img width="1949" height="1211" alt="Blend Space" src="https://github.com/user-attachments/assets/ae0a3737-7e41-40ac-88c4-ec6f58c6c5e8" />
<br>
블렌드 스페이스를 사용하여 IDLE, WALK, RUN 을 구분했다<br>
Blend Space 1D로도 가능했지만 나중에 추가할 수 있으므로 일반 Blend Space를 사용했다
<br><br>

<img width="1014" height="409" alt="ABP 이벤트 그래프" src="https://github.com/user-attachments/assets/15cef8df-7746-4c1f-bb27-767a9c6f754e" />
<br>
달리기 속도를 구할땐 Z축의 값은 필요 없으므로 X,Y의 속도를 가져와서 speed에 저장한다<br>
Movement 컴포넌트에선 플레이어가 떠있는지를 가져와서 IsFalling에 저장한다
<br><br>

<img width="694" height="324" alt="Locomotion" src="https://github.com/user-attachments/assets/55cf9d2d-2851-403a-9b1b-248bbeb52586" />
<br>
<img width="513" height="436" alt="StateMachine" src="https://github.com/user-attachments/assets/a2a2d79a-e6fb-4228-9fb5-1a6f17c10515" />
<br>
<img width="748" height="381" alt="Speed 이용" src="https://github.com/user-attachments/assets/e916cb2b-d385-497c-8de8-f533ed222263" />
<br>
<img width="497" height="225" alt="IsFalling 이용" src="https://github.com/user-attachments/assets/772b764c-e79e-4ae6-9870-d195d1e96ee8" />
<br>
StateMachine을 생성하고 연결했다<br>
땅에 붙어서 행동하는 IDLE, WALK, RUN 와 JUMP 밖에 없으므로 상태를 두개로 구분했다.<br>
OnSpace 에선 앞에서 만든 BlendSpace를 이용하고 Speed로 달리기 애니메이션을 처리한다<br>
Jump는 IsFalling을 조건으로 이용해서 점프 애니메이션을 출력했다
<br><br><br><br>


### Enhanced Input System의 장점
걷기용 IMC, 자동차용 IMC를 분리하고 동작을 따로 정의해서 플레이어 조작을 쉽게 교체할 수 있다
<br><br><br><br>



+ ### 빙판 플랫폼
````
Movement Component로 마찰제어, Overlap 겹침 구간 처리
````
<br>
<img width="1306" height="481" alt="빙판 플랫폼" src="https://github.com/user-attachments/assets/3f385113-e441-4b10-8589-14f54974bb16" />
<br>
처음에 빙판을 구현할 때 위와 같이 구현했고 End Overlap에서 마찰을 원래로 되돌렸다<br>
이렇게 구현하면 하나의 빙판에 대해선 문제가 없지만 작은 빙판을 여러개 붙였을 때<br>
빙판사이에 플레이어가 겹칠 경우 미끄러지지 않는 문제가 있다<br><br>

2번 빙판에서 Begin Overlap으로 마찰력을 줄이지만<br>
1번 빙판에서 End Overlap으로 마찰력을 원래대로 되돌리기 때문이다
<br><br><br>

<img width="1014" height="163" alt="빙판 플랫폼 개선" src="https://github.com/user-attachments/assets/c6b79ff9-f0c4-4232-b6e3-e4888439c757" />
<br>
그래서 위 사진과 같이 노드를 고쳐서 문제를 해결했다<br>
해결 방법은 아래와 같다<br>

    1. 플레이어는 올라탄 빙판의 수를 기록한다
    2. 올라타있는 빙판의 수가 1일때(맨처음 올라탈때) 마찰력을 줄인다
    3. 올라타있는 빙판의 수가 0일때(모든 빙판에서 내려올때) 마찰력을 되돌린다
이렇게 로직을 바꾸니 작은 빙판을 여러개 이어붙여도 끝까지 미끄러질 수 있었다
<br><br><br><br>




+ ### 사라지는 플랫폼
````
Dynamic Material Instance를 이용한 투명화
````
<br>
<img width="380" height="601" alt="블렌드 모드 = Translucent" src="https://github.com/user-attachments/assets/be64d0c8-2f9a-4c2b-93b4-14ab1723c187" />
<br>
<img width="796" height="521" alt="Scalar Parameter" src="https://github.com/user-attachments/assets/5c244597-6d44-4656-b7b5-80e7f11bdc9e" />
<br>
우선 투명화를 하기위해 머티리얼의 블렌드 모드를 Translucent로 바꾸고<br>
Scalar Parameter 노드를 추가해 Opacity에 연결했다
<br><br>

<img width="902" height="398" alt="MID" src="https://github.com/user-attachments/assets/e6a88d10-8939-4e51-8e6f-ef0c214db9bd" />
<br>
위에서 저장한 값을 사용하기위해 동적 머티리얼 인스턴스를 시작할 때 저장한다<br><br>

<img width="946" height="410" alt="소멸 로직" src="https://github.com/user-attachments/assets/e95dfecb-190a-46f3-8b1e-62adecf87674" />
<br>
플레이어와 충돌하면 Do Once로 TimeLine을 한번만 실행하도록 한다<br>
앞에서 Opacity라는 이름으로 Scalar Parameter를 만들었는데 이를 Set Scalar Parameter Value 노드로 변경해준다<br><br>

완전히 투명해졌다면 가시성과 충돌을 해제하고 앞에서 조절한 Opacity값으로 되돌린다<br>
이후에 일정 시간 후 가시성과 충돌을 되돌려서 발판이 생겨난것처럼 처리한다<br><br><br>

### Do Once 처리
````
사라지는 플랫폼은 재생성하므로 해당 로직을 다시 처음부터 처리할 필요가 있다
TimeLine은 별도의 처리가 없다면 한 사이클이 끝나면 Time 값이 끝에 가 있으므로 처음부터 처리하려면 Play from Start 핀에 연결해야한다
이때 플레이어가 여러면 충돌을 했을 때 발판이 투명해지다가 다시 처음부터 투명해지는 오류가 생길 수 있으므로
Do Once를 이용해서 해당 문제가 생기지 않도록 했다
````


### 액터의 재사용
````
Destroy, Spawn 대신 Hidden, Enable Collision을 사용했는데 그 이유는
생성과 소멸은 무거운 작업이므로 기존 액터를 숨겨뒀다가 재사용하는 방식이 더 효율적이기 때문이다
````
<br><br><br><br>




+ ### 엘레베이터 플랫폼
````
Custom Event를 이용한 엘레베이터 구현
````
<br>
<img width="818" height="614" alt="엘레베이터 버튼" src="https://github.com/user-attachments/assets/f79e5a98-feab-4724-ad37-cf34a798db8d" />
<br>
<img width="1144" height="818" alt="엘레베이터 플랫폼" src="https://github.com/user-attachments/assets/d08c587b-2f42-4fe1-b81f-c5210ee4ed2c" />
<br>
엘레베이터는 이전에 Trigger box 와 돌멩이 함정을 분리했던 것처럼<br>
엘레베이터 호출 버튼과 플랫폼으로 분리했다<br><br>

엘레베이터에선 플레이어가 탑승했는지, 버튼에선 플레이어가 누르고 있는지를 Overlap Event로 확인한다<br>
처음엔 Tick Event로 처리했으나 매 프레임 필요없는 연산을 계속 한다는 문제가 있었다<br>
이를 Overlap 이벤트가 발생하면 그때 커스텀 이벤트를 호출하는 방식으로 해결했다<br><br>

Custom Event는 버튼의 눌림, 엘레베이터 탑승 상태를 확인해서 Timeline으로 상승/하강을 처리한다<br>
Play, Stop, Reverse 핀을 이용해서 타는 도중에 점프하면 멈추고 다시 올라타면 상승하도록했다<br>
버튼 또한 플레이어가 밟으면 TimeLine으로 버튼 부분이 눌려서 내려가도록했다<Br>
