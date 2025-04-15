```cpp
상품을 판매하는 온라인 마트가 있다.
상품은 ID로 구별되고 품목, 제조사, 가격 정보를 가지고 있다.
품목과 제조사는 1부터 5까지의 정수로 주어진다.
온라인 마트에서 상품을 판매할 수 있고 상품의 판매를 종료할 수 있다.
또한, 같은 품목과 제조사를 가진 상품들에 대해서 가격을 특정 금액만큼 할인할 수 있다.
예로 품목이 1이고 제조사가 2인 상품에서 대해서 5만큼 할인한다면 품목이 1이고 제조사가 2인 모든 상품의 가격이 5만큼 감소한다.
만약, 할인되어 가격이 0 또는 음수가 된 상품은 더 이상 판매가 의미 없는 상품이 되어 판매가 종료된다.
또한, 사용자에게 특정 조건을 만족하는 상품들에 대해서 가격이 낮은 순서로 최대 5개 상품을 검색하여 보여준다.
조건은 전체 상품, 특정 품목, 특정 제조사로 3가지 방식으로 줄 수 있다. 가격이 같은 경우 상품 ID가 더 낮은 것이 우선이다.

void init()
테스트 케이스에 대한 초기화 함수. 각 테스트 케이스의 맨 처음 1회 호출된다.
초기에 판매되는 상품은 없다.

int sell(int mID, int mCategory, int mCompany, int mPrice)
ID가 mID이고 품목이 mCategory이고 제조사가 mCompany이고 가격이 mPrice인 상품을 판매 시작한다.
판매 시작한 후 품목이 mCategory이고 제조사가 mCompany인 판매 중인 상품의 개수를 반환한다.
함수가 호출 시 전달되는 mID 값은 이전 호출에서 이미 전달된 값이 아님을 보장한다.
Parameters
  mID : 추가할 자료의 ID (1 ≤ mID ≤ 1,000,000,000)
  mCategory : 상품의 품목 (1 ≤ mCategory ≤ 5)
  mCompany : 상품의 제조사 (1 ≤ mCompany ≤ 5)
  mPrice : 상품의 가격 (1 ≤ mPrice ≤ 1,000,000)
Return Value
  같은 품목과 제조사를 가진 판매 중인 상품의 개수

int closeSale(int mID)
ID가 mID인 상품을 판매 종료한다.
판매를 종료할 때 상품의 가격을 반환한다. 만약, 판매하고 있지 않은 상품이거나 판매가 종료된 상품인 경우 -1을 반환한다.
Parameters
  mID : 판매를 종료할 상품의 ID (1 ≤ mID ≤ 1,000,000,000)
Return Value
  판매를 종료할 때 상품의 가격을 반환하고 상품을 판매하지 않는 경우 -1.

int discount(int mCategory, int mCompany, int mAmount)
품목이 mCategory이고 제조사 mComapny인 모든 상품의 가격을 mAmount 만큼 낮춘다.
만약, 낮춘 가격이 0이거나 음수가 되면 해당 상품은 판매 종료한다.
가격을 낮춘 후 품목이 mCategory이고 제조사가 mCompany인 판매되고 있는 상품의 개수를 반환한다.
Parameters
  mCategory : 할인하고자 하는 상품의 품목 (1 ≤ mCategory ≤ 5)
  mCompany : 할인하고자 하는 상품의 제조사 (1 ≤ mCompany ≤ 5)
  mAmount : 할인되는 금액 (1 ≤ mAmount ≤ 1,000,000)
Return Value
  품목과 제조사가 mCategory, mCompany인 판매 중인 상품 개수

RESULT show(int mHow, int mCode)
mHow에 따라 조건을 만족하는 상품 중 가격이 낮은 순서로 최대 5개의 상품을 RESULT 구조체에 저장하고 반환한다. 만약 가격이 같은 경우 상품 ID가 더 적은 값을 가진 상품이 우선한다.
① mHow = 0인 경우 모든 상품에 대해서
② mHow = 1인 경우 품목이 mCode인 모든 상품에 대해서
③ mHow = 2인 경우 제조사가 mCode인 모든 상품에 대해서
판매 종료된 상품은 제외한다.
반환할 때 저장된 상품의 개수를 RESULT.cnt에 저장하고 i번째 상품의 ID를 RESULT.IDs[i – 1]에 저장한다. (1 ≤ i ≤ RESULT.cnt)
조건에 만족하는 상품이 없는 경우 RESULT.cnt에 0을 저장한다.
mHow = 0일 때 mCode는 0인 값이 들어오지만 의미가 없다.
mHow = 1 또는 2일 때 mCode는 1 이상 5 이하의 정수 값이다.
Parameters
  mHow : 검색 조건 (0 ≤ mHow ≤ 2)
  mCode : 검색 값 (0 ≤ mCode ≤ 5)
Return Value
  검색 조건에 만족하는 상품 중 가격이 낮은 순서로 최대 5개의 상품
```
