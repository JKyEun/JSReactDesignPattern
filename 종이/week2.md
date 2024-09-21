#### 7.2 생성자 패턴

- class를 만들어서 생성자 패턴을 자주 사용하시나요?

- 회사 코드에 API response를 클래스 인스턴스 객체로 생성해서 return하는 코드가 있습니다. Stocks Class getter에는 Stock 인스턴스 객체를 생성하는 방식으로 두 클래스 간 연관성이 있는데 자주 쓰이는 데이터라 코드 추가할 일이 많아요. 관련 코드가 모여있으니 응집도가 높아 좋다는 생각이 들었습니다.

```javascript
const getSpecitpicStock = async (stockId: string) => {
  const res = await itemApis.fetchSpecitpicStock(id);
  const { data } = res;
  return new Stock(data);
};

const getSpecitpicStocks = async (stockIdList: string[]) => {
  const res = await itemApis.fetchSpecitpicStocks(id);
  const { data } = res;
  return new Stocks(data);
};

export class Stocks {
  private datas: StocksData[] = [];
  constructor(stockDatas: StocksData[]) {
    this.datas = stockDatas;
  }

get stocks() {
    return this.datas.map(data => new Stock(data));
  }

// ... 중략
}
```
