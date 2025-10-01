# Learning-Centered_MultiGame

## <개요>
5인 개발로 진행되는 언리얼 엔진을 활용한 멀티게임으로서 기본적인 메쉬 설정등은 제외하고 멀티와 관련된 핵심 기술에 대해서만 기술함.


## 플레이어 상태 동기화
멀티 게임에서 중요한 점은 서버와 클라이언트 플레이어의 상태가 똑같이 보여야 한다는 것이다.  
예를들어 클라이언트 플레이어가 앉기 상태로 돌입하여 속도를 낮추고 싶다면 서버와 다른 클라이언트쪽에서도 그 클라이언트의 속도가 낮춰져야 한다.  
따라서 변수가 복제될수 있게 설정하고, 그 변수가 서버쪽에서 바뀜으로 인해서 다른 클라이언트들도 모두 똑같이 복제되어 적용될 수 있도록 한다.  

1. 변수를 복제되게끔 설정한다. 이때, 단순 복제가 아니라 ReplicatedUsing을 사용하여 변수가 복제될때 클라이언트측에서 자동으로 실행될 함수를 지정해준다.

<img width="758" height="70" alt="앉기 복제 변수 및 OnRep 설정" src="https://github.com/user-attachments/assets/e77628d7-dcd5-4405-972f-5e499362992f" />

<img width="944" height="218" alt="복제 변수 설정" src="https://github.com/user-attachments/assets/e7a5425b-3e19-4771-afb0-3ae9c9d14201" /><br><br>

2. 서버RPC를 사용하여 서버쪽에서 변수를 바꿀수 있게끔 한다.  
서버RPC로 실행한 함수는 서버쪽에서만 실행되고 클라이언트한테는 실행되지 않는다는것을 기억해둔다.  
따라서 현재 상황에서는 서버RPC로 인해 서버에 있는 클라이언트 캐릭터 인스턴스의 속도만 변경되며, 복제변수가 클라이언트들한테 복제된다.
<img width="1116" height="75" alt="서버RPC선언" src="https://github.com/user-attachments/assets/baef091c-688f-403d-a568-3f11f4d070f4" />
<img width="1116" height="400" alt="앉기 RPC 코드" src="https://github.com/user-attachments/assets/f26d1110-08ef-4119-9aba-c44cd5c9c6b8" /><br><br>

3. 1번 과정에서 복제변수에 지정해두었던 함수를 정의함으로서 서버에서만 바뀌었던 속도를 클라이언트들도 똑같이 바뀌게 해준다.
<img width="1219" height="229" alt="앉기 OnRep" src="https://github.com/user-attachments/assets/02ff7820-7782-443d-a825-dfe9fea2a856" /><br><br>

4. 앉기 키를 누르면 서버RPC를 실행할 수 있게끔 서버RPC를 바인딩한다.
<img width="1372" height="349" alt="크라우치 바인딩" src="https://github.com/user-attachments/assets/60f14826-807a-4629-a468-4138118bad14" /><br><br>

<결과>  
클라이언트가 앉기 키를 누르면 서버와 다른 클라이언트에서도 앉기 속도로 동기화되어 이동하는 것을 확인할 수 있다.
![정상이동](https://github.com/user-attachments/assets/bca6c457-71a1-4d8d-b9b6-414183340a6b)<br><br>

## 플레이어 공격 모션 실행
공격 모션의 경우도 복제 변수처럼 변수가 바뀌었을때 지정된 함수를 이용하여 공격 모션을 실행할 수 있긴 하다.  
그러나 공격모션같은 경우는 한번 실행하고 나면 그만인 행동이다.  
따라서 굳이 변수를 복제하여서 상태를 바꾸고 공격 애니메이션 실행후, 다시 애니메이션 실행할수 있게끔 변수를 바꾸고 복제하는 것은 다소 비효율적이다.
이러한 경우는 서버RPC와 NetMulticastRPC를 조합하여 일회성으로 애니메이션을 한번만 실행하고 끝내는 것이 효율적이다.

1. 공격 애니메이션을 실행할 NetMulticastRPC를 선언하고 정의한다.  
   여기서는 공격과 관련한 중요한 모션이기에 Reliable로 선언해주었지만  
   보통은 게임플레이시 있으면 좋지만 없다고해서 게임 진행에 영향을 주지 않는 사운드 및 MuzzleEffect같은 것에 사용함으로 Unreliable로 많이 선언한다.
<img width="402" height="79" alt="밀리어택 넷멀티rpc" src="https://github.com/user-attachments/assets/67d50991-525a-4527-a342-00d1ca7e03e8" />
<img width="1047" height="265" alt="밀리어택 넷멀티rpc 코드" src="https://github.com/user-attachments/assets/2474a932-ce74-4994-9aa8-f77c99e8737b" /><br><br>

2. NetMulticastRPC는 서버쪽에서 실행해야 한다. 따라서 NetMulticastRpc를 실행할 서버RPC를 선언하고 정의한다.  
NetMulticastRPC는 서버RPC와는 다르게 서버와 클라이언트 양쪽 모두에서 실행되는 함수이다.
<img width="691" height="69" alt="밀리어택 서버rpc" src="https://github.com/user-attachments/assets/fce39560-850a-48cc-abd2-401437bf57bb" />
<img width="1230" height="247" alt="밀리어택 서버rpc 코드" src="https://github.com/user-attachments/assets/f79e6d8a-1f84-477f-8e1d-b04ee45dfe5d" /><br><br>

3. 공격 키를 누르면 서버RPC를 실행할 수 있게끔 서버RPC를 바인딩한다.
<img width="1368" height="353" alt="밀리어택 바인딩" src="https://github.com/user-attachments/assets/0f40d0db-908f-4295-9ed3-3f68dd209d06" /><br><br>

<결과>  
클라이언트가 공격 키를 누르면 서버와 다른 클라이언트에서도 공격 모션을 실행하는 것을 확인할 수 있다.  
다시 순서대로 정리해보자면 클라이언트가 공격 키를 누르면 서버RPC가 실행되고, 그 서버RPC의 내용은 NetMulticastRPC를 실행하는것이다.  
NetMulticastRPC는 서버와 클라이언트 양쪽 모두에서 실행되므로 서버와 모든 클라이언트에서 공격 모션이 동시에 실행되는 것이다.
![근접공격](https://github.com/user-attachments/assets/297f7717-0a85-433d-9c4c-02184cfe61af)<br><br>

## 인터페이스를 이용한 상호작용
멀티게임에서도 다른 오브젝트와 상호작용을 할 수 있다.  
이때, 상호작용했다는것을 다른 플레이어도 볼 수 있게 하려고한다.  
또한 상호작용 가능한 오브젝트를 여러 종류 만들기 위해 인터페이스를 사용한다.  

1. 인터페이스 헤더파일을 작성한다.
<img width="1062" height="475" alt="인터페이스 헤더파일" src="https://github.com/user-attachments/assets/61c9fa20-457b-4315-89ea-667561a5387e" /><br><br>

2. 인터페이스 함수를 사용할 다른 액터 클래스에서 인터페이스를 상속받아 구현한다.  
   또한 복제 변수 선언 및 인터페이스 함수 재정의를 위한 선언등을 한다.
<img width="998" height="84" alt="인터페이스 상속 받음" src="https://github.com/user-attachments/assets/86d6836a-eed4-46bc-aae1-5ea74632e1dd" />
<img width="912" height="283" alt="인터페이스 박스 헤더파일" src="https://github.com/user-attachments/assets/e351847b-94de-4751-82ec-a83d13e6c657" /><br><br>

3. 다른 클라이언트에서도 확인 가능하도록 생성자에서 액터를 복제할 수 있게 해준다.
<img width="1236" height="281" alt="인터페이스 박스 생성자" src="https://github.com/user-attachments/assets/ae935a7c-a706-4ea9-afd6-d859b6a7e603" /><br><br>

4. 인터페이스 함수를 재정의하며, 그로인해 오브젝트가 해야할 상호작용을 정의한다.
<img width="801" height="206" alt="인터페이스 박스 함수 구현" src="https://github.com/user-attachments/assets/5ed26da1-6d80-4226-8372-0120dd3446a7" />
<img width="968" height="170" alt="인터페이스 박스 틱" src="https://github.com/user-attachments/assets/bc018685-a528-4ff8-97ae-10c4f9758169" /><br><br>


