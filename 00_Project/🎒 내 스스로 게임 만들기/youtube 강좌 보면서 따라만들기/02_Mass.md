이번 영상에서는 플레이어가 먹는 양분을 구현했다. 

먼저 Mass라는 Tag를 만들고 Mass 객체를 만든 다음 Prefab화 시킨 후 Mass 태그를 삽입 시켰다. 그리고 Game에 곳곳에 배치함 

그 다음에 PlayerEatMass.cs를 구현했다. 여기서 구현한 메소드는
- UpdateMass() 
	- Tag가 Mass인 Object를 찾는 함수 
- RemoveMass()
	- 질량이 Mass인 것들을 삭제하는 함수
- CheckMass()
	- 질량이 플레이어와 닿았는지 확인하는 함수 
- PlayerEat() 
	- 플레이어의 크기를 키우는 함수 

코드는 다음과 같다. 
```cs
using System.Collections;  
using System.Collections.Generic;  
using UnityEngine;  
  
public class PlayerEatMass : MonoBehaviour  
{  
    // Mass 변수는 플레이어가 성장할 수 있게 해주는 주체 입니다.  
    public GameObject[] Mass;  
  
    public void UpdateMass()  
    {        // Hierarchy 창에서 Tag가 Mass인 Object를 찾는다.   
// 그리고 값을 넣는다.  
        Mass = GameObject.FindGameObjectsWithTag("Mass"); // 너는 Mass를 위해 새로운 태그를 만들어야 한다.   
}  
  
    public void AddMass(GameObject MassObject)  
    {        List<GameObject> Masslist = new List<GameObject>();  
        for (int i = 0; i < Mass.Length; i++)  
        {            Masslist.Add(Mass[i]);  
        }                Masslist.Add(MassObject);  
        Mass = Masslist.ToArray();  
    }    public void RemoveMass(GameObject MassObject)  
    {        // MassList를 생성   
List<GameObject> Masslist = new List<GameObject>();  
        // Mass 배열과 MassList를 동기화   
for (int i = 0; i < Mass.Length; i++)  
        {            Masslist.Add(Mass[i]);  
        }        // MassList의 값을 수정   
Masslist.Remove(MassObject);  
        // MassList의 값과 Mass의 값을 동기화   
Mass = Masslist.ToArray();  
    }  
    public void CheckMass()  
    {        for (int i = 0; i < Mass.Length; i++)  
        {            // Transform 타입은 게임 오브텍트의 위치, 회전 및 크기를 나타낸다.  
            Transform m = Mass[i].transform;  
            // 현재 객체와 질량 요소 간의 거리가 현재 객체의 크기의 절반보다 적거나 같은지 확인한다.   
// 질량 요소의 간격을 / 2를 해준 이유는 반지름을 구하기 위해서이다.   
if (Vector2.Distance(transform.position, m.position) <= transform.localScale.x / 2)  
            {                // 물체에 닿았기 때문에 mass를 삭제한다.   
RemoveMass(m.gameObject);  
                // eat -> 플레이어 먹음 처리   
PlayerEat();  
                // destroy mass -> 해당 질량 삭제시키기   
Destroy(m.gameObject);  
            }        }    }    void Start()  
    {        UpdateMass(); // 현재 Hirearchy에 잇는 Mass들을 모두 불러오기   
          
// InvokeRepeating은 methodName을 time(초)후에 실행하면 repeateRate 주기마다 반복적으로 호출합니다.  
        InvokeRepeating("CheckMass", 0, 0.1f);  
            }  
  
  
    public void PlayerEat()  
    {        // 0.05f씩 크기 키워주기   
transform.localScale += new Vector3(0.05f, 0.05f, 0.05f);  
    }}
```


그리고 player의 크기에 따라서 스피드는 낮아줘야 하기 때문에 
PlayerMOvements.cs에 speed를 적용하는 
```cs
float speed_ = speed / transform.localScale.x;
```

이런 코드를 추가하여 MoveToward함수에 인자로 사용하였다.