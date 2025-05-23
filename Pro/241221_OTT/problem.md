```cpp
N 명의 사용자가 가입한 OTT 시스템이 있다.
이 시스템은 다음과 같은 기능을 제공한다.

   ① 영화를 등록한다.

   ② 등록된 영화를 삭제한다.

   ③ 사용자는 영화를 시청하고 시청한 영화에 대한 평점을 준다.

   ④ 사용자가 최근 시청한 영화들을 참고하여 사용자에게 최대 5개의 영화를 추천한다.

 

 

각 사용자는 1부터 N까지의 고유 번호가 부여되어 있고 그 고유 번호로 구별된다.

 

 

각 영화는 ID, 장르를 가지고 있다. 영화는 ID로 구분되고 같은 ID를 가지는 영화는 없다. 장르는 1부터 5까지 수로 표현된다.

 

 

또한, 영화는 총점을 가진다.

사용자가 영화를 시청하고 시청한 영화에 대해서 평점을 주면 그 영화의 총점은 사용자가 준 평점만큼 증가한다.

 

 

각 사용자는 시청 목록을 가지고 있다. 사용자가 영화를 시청하면 그 영화가 시청 목록에 추가된다.

또한, 사용자가 이미 시청한 영화를 다시 시청하지는 않는다.

 

 

영화가 삭제될 수도 있다. 삭제되면 더 이상 그 영화는 시청될 수도 추천될 수도 없고 사용자의 시청 목록에서도 삭제된다.

 

 

OTT 서비스에서는 사용자에게 다음과 같은 규칙으로 영화를 추천한다.

  ⓐ 사용자가 이미 시청했거나 삭제된 영화는 제외한다.

  ⓑ 시청 목록에 있는 가장 최근 시청한 최대 5개의 영화들 중에서 사용자가 준 평점이 가장 높은 영화와 같은 장르의 영화만 추천한다.

       가장 높은 평점을 준 영화가 여러 개이면 그 중에서 가장 최근에 시청한 영화와 같은 장르의 영화만 추천한다.

  ⓒ 만약, 사용자의 시청 목록에 어떤 영화도 없는 경우 장르에 상관없이 모든 영화에 대해서 추천한다.

  ⓓ 위 조건을 만족하는 영화들 중에서 총점이 가장 높은 순으로 최대 5개를 추천한다.

       만약 총점이 같은 경우 더 최신에 등록한 영화가 우선 순위가 더 높다.

```
