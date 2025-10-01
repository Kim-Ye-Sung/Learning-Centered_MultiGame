# Learning-Centered_MultiGame

## <개요>
5인 개발로 진행되는 언리얼 엔진을 활용한 멀티게임으로서 기본적인 메쉬 설정등은 제외하고 멀티와 관련된 핵심 기술에 대해서만 기술함.


## 플레이어 속도 동기화
멀티 게임에서 중요한 점은 서버와 클라이언트 플레이어의 상태가 똑같이 보여야 한다는 것이다.  
예를들어 클라이언트 플레이어가 앉기 상태로 돌입하여 속도를 낮추고 싶다면 서버와 다른 클라이언트쪽에서도 그 클라이언트의 속도가 낮춰져야 한다.  
따라서 변수가 복제될수 있게 설정하고, 그 변수가 서버쪽에서 바뀜으로 인해서 다른 클라이언트들도 모두 똑같이 복제되어 적용될 수 있도록 한다.  

1. 변수를 복제되게끔 설정한다. 이때, 단순 복제가 아니라 ReplicatedUsing을 사용하여 변수가 복제될때 클라이언트측에서 자동으로 실행될 함수를 선언해준다.

<img width="758" height="70" alt="앉기 복제 변수 및 OnRep 설정" src="https://github.com/user-attachments/assets/e77628d7-dcd5-4405-972f-5e499362992f" />

<img width="944" height="218" alt="복제 변수 설정" src="https://github.com/user-attachments/assets/e7a5425b-3e19-4771-afb0-3ae9c9d14201" />



    
2. 서버RPC를 사용하여 서버쪽에서 변수를 바꿀수 있게끔 한다.
<img width="1116" height="75" alt="서버RPC선언" src="https://github.com/user-attachments/assets/baef091c-688f-403d-a568-3f11f4d070f4" />
<img width="1116" height="400" alt="앉기 RPC 코드" src="https://github.com/user-attachments/assets/f26d1110-08ef-4119-9aba-c44cd5c9c6b8" />
