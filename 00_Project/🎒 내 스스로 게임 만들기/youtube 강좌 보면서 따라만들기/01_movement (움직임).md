Game Hierachy 참에 Player를 추가한 후 
코드를 추가해 주었다.

PlayerMovements.cs
```cs
public class PlayerMovements : MonoBehaviour  
{  
    public float speed = 5f;  
    void Start()  
    {            }  
  
    void Update()  
    {        // 마우스 위치를 2D 월드 좌표로 반환합니다.  
        Vector2 direction = Camera.main.ScreenToWorldPoint(Input.mousePosition);  
        // 현재 객체의 위치에서 마우스 위치로 이동하는 방향으로 이동합니다.  
        // speed는 이동 속도를 나타내며 Time.deltaTime은 프레임 간 시간 간격입니다. -> Time.deltaTime을 곱하여 프레임 속도에 의존하지 않도록 한다.   
transform.position = Vector2.MoveTowards(transform.position, direction, speed * Time.deltaTime);  
    }}
```

여기서 Time.deltaTime이 나오게 되는데, 이것은 컴퓨터간에 프레임 처리 속도가 다르다. 그래서 결과가 다르게 나올 수 있는데, 프레임 간 시간간격을 구해 이것을 해결하는 방식으로 처리했으며, 프레임 간 시간간격을 구할 수 있는것이 time.deltaTime인 것이다.

참고 https://inyongs.tistory.com/18

---

중요한 것은 이번 챕터에서는 Player의 움직임을 구현헀다.