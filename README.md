# TripList-iOS

해당 앱은 `VIPER 패턴`을 적용하기 위한 `튜토리얼 앱`으로 학습 목적으로 생성하였습니다. 

모든 예제 코드와 내용에 대한 저작권([링크](https://www.kodeco.com/29416318-getting-started-with-the-vip-clean-architecture-pattern#toc-anchor-001))은 원저자에게 있습니다. 

## How to run

```
> cd finished
> open Viper.xcodeproj
```

## App Apperance
<div>
<img src="https://user-images.githubusercontent.com/111719007/231434438-dda8050f-1cdd-4d4c-ab86-883f77aeca82.png" height="400"/>
<img src="https://user-images.githubusercontent.com/111719007/231434426-17d803ef-fa19-4f8b-8ac3-5a9ee70ad8d0.png" height="400"/>
<img src="https://user-images.githubusercontent.com/111719007/231433826-9c8e3dbe-b9c6-4665-b2e5-4bf9de51f91b.png" height="400"/>
<img src="https://user-images.githubusercontent.com/111719007/231433868-070c1ac9-c7a2-499e-87c8-c4f71505c8db.png" height="400"/>
</div>

## VIPER (View-Interactor-Presenter-Entity-Router)

<img src="https://user-images.githubusercontent.com/111719007/231433942-38ffc5fe-c32d-4d2e-be6e-b3bacdcb6a4d.png" height="250"/>

- `VIPER 패턴`은 `클린 아키텍쳐`를 구현하는 방법중 하나로, `비지니스 로직`과 `UI`를 `분리`함으로써 `유지 보수성`과 `확장성`을 `향상`시키는 `디자인 패턴`입니다.
- 주로 대규모 프로젝트나 `복잡한 UI 로직`이 `필요한` 앱에서 주로 사용됩니다. 
- 앱의 코드를 `다양한 모듈`로 나누어 각 `모듈`이 `자체적`으로 `동작`하도록 만듭니다.
- VIP 패턴과 마찬가지로 `데이터의 흐름`은 `한 방향`으로만 이루어지며, 데이터가 변경될 때는 오직 Presenter나 Interactor를 통해서만 가능합니다.
- View는 Presenter를 통해서만 데이터를 전달받으며, Interactor는 Entity를 통해서만 데이터에 접근합니다.
- `단방향 아키텍처 패턴`이란, `데이터 흐름`이 `일방향`이며 각 구성 요소가 서로 분리되어 있어 변경에 대한 영향을 최소화하고 유지보수성을 향상시키는 패턴입니다.
- 위와 같은 데이터 흐름은 각 `구성요소`가 `독립적`으로 `테스트`될 수 있게 해주고, 코드의 가독성과 유지보수성을 높여줍니다. 

<pre>1. View: 사용자 인터페이스를 처리하며, 유저의 액션을 Presenter에 전달합니다.
2. Interactor: Presenter와 데이터 간의 중재자 역할을 하며, Presenter에서 지시를 받습니다.
3. Presenter: 아키텍처의 "신호등" 역할을 하며, 데이터를 View와 Interactor 사이에서 전달하며, 사용자 동작을 수행하고 사용자를 View 간 이동하기 위해 Router를 호출합니다.
4. Entity: 앱의 데이터 모델을 정의합니다.
5. Router: 화면 간 탐색을 처리합니다. 즉, VIPER 모듈 간의 이동을 처리하며, 다른 모듈로의 데이터 전달을 담당합니다.</pre>

- 각 모듈의 책임을 분리하여 각각이 독립적으로 작동할 수 있도록 하는 것이 핵심입니다.
- 이러한 모듈화 방식을 통해 `유지보수성` 높이고 `코드 복잡도`를 낮출 수 있습니다.

## How to apply

### Entity(개체) 정의하기

<img src="https://user-images.githubusercontent.com/111719007/231440766-dfda7ea4-5942-441f-b977-252560c62331.png" height="80"/>

- Entities 폴더내에서 Trip, Waypoint, DataModel의 앱의 데이터 개체를 생성하였습니다.

1. DataModel: 앱에서 사용하는 모든 데이터 모델을 정의한 클래스이며, Trip, Waypoint, Categroy등의 데이터 모델 클래스를 포함합니다.
2. Trip: 여행 정보를 담은 데이터 모델 클래스이며, id, Destination등과 같은 여행 정보에 대한 속성을 포함합니다.
3. Waypoint: 여행 중 거쳐가는 지점 정보를 담은 모델 클래스이며, id, latitude, longitude 등 지점 정보에 대한 속성을 포함합니다.

### 인터렉터 만들기
→ 인터랙터 클래스가 생성하고 나중에 사용할 DataModel을 할당합니다.

```Swift
class TripListInteractor {
  let model: DataModel

  init (model: DataModel) {
    self.model = model
  }
}
```

### 프리젠터 만들기
→ Presenter는 UI에 데이터를 제공하고 사용자 동작을 중재하는 역할을 합니다.

```Swift
/* Presenter 클래스는 ObservableObject 프로토콜을 채택하고, interactor 객체를 참조합니다. */

import SwiftUI
import Combine

class TripListPresenter: ObservableObject {
  private let interactor: TripListInteractor

  init(interactor: TripListInteractor) {
    self.interactor = interactor
  }
}
```

```Swift
/* @Published 속성 래퍼를 사용하여 사용자가 뷰에서 볼 수 있는 여행 목록을 저장하는 trips 배열을 정의합니다.
   데이터 모델이 변경될 때마다 Presenter의 trips 배열도 업데이트됩니다. */

@Published var trips: [Trip] = []
```

```Swift
/* 이 Set은 Presenter의 수명에 맞춰서 Combine subscriptions을 보관하므로, Presenter가 종료될 때 해당 subscriptions도 정리됩니다. */

private var cancellables = Set<AnyCancellable>()
```

```Swift
/* 데이터 모델의 여행 컬렉션에 대한 변경 사항을 추적하는 게시자를 생성하고,
   해당 값은 여행 컬렉션에 저장되어 데이터가 변경 될때 업데이트된 상태로 유지하는 링크를 생성하고,
   나중에 취소할 수 있도록 취소가능 Set에 저장됩니다.*/

interactor.model.$trips
  .assign(to: \.trips, on: self)
  .store(in: &cancellables)
```

### 뷰 만들기
→ 첫 번째 뷰인 여행 목록을 구현합니다.

```Swift
/* TripListView에 TripListPresenter 속성을 추가하여 프리젠터를 뷰에 연결합니다. */
@ObservedObject var presenter: TripListPresenter
```

```Swift
/* 다음으로 TripListView_Previews.previews를 아래와 같이 수정하여 미리보기를 수정합니다. */
let model = DataModel.sample
let interactor = TripListInteractor(model: model)
let presenter = TripListPresenter(interactor: interactor)
return TripListView(presenter: presenter)
```

```Swift
/* TripListView의 Body 부분을 아래와 같이 수정하여 여행이 열거된 List가 생성하고 각각에 대해 TripListCell이 생성됩니다. */
List {
  ForEach (presenter.trips, id: \.id) { item in
    TripListCell(trip: item)
      .frame(height: 240)
  }
}
```

### 뷰에 모델 적용하기
→ 위에서는 View를 구성하기 위해 Presenter 통해 Entity에서 Interactor로 이어지는 데이터 흐름을 살펴보았는데, 아래의 구현을 통하여 Presenter에서 View로 데이터를 보낼 수 있습니다.

```Swift
/* TripListInteractor에 add와 delete 메서드를 추가합니다. */

func addNewTrip() {
  model.pushNewTrip()
}

func deleteTrip(_ index: IndexSet) {
  model.trips.remove(atOffsets: index)
}
```

```Swift
/* TripListPresenter에 interactor의 add와 delete 메서드를 입력받아 생성과 삭제를 구현하는 메서드를 만듭니다. */

func makeAddNewButton() -> some View {
  Button(action: addNewTrip) {
    Image(systemName: "plus")
  }
}

func addNewTrip() {
  interactor.addNewTrip()
}

func deleteTrip(_ index: IndexSet) {
  interactor.deleteTrip(index)
}
```

```Swift
/* TripListView에서 .navigationBarItems 함수를 사용하여 버튼과 제목을 네비게이션 바에 추가합니다. */

.navigationBarTitle("Roadtrips", displayMode: .inline)
.navigationBarItems(trailing: presenter.makeAddNewButton())

/* TripListView에서 .onDelete 함수를 사용하여 리스트의 항목에서 swipe to delete 동작을 가능하게 하고,
   항목 삭제 액션은 presenter.deleteTrip(_:) 함수로 전달됩니다. */

.onDelete(perform: presenter.deleteTrip)
```

```Swift
/* TripListView_Previews에서 NavigationView로 감싸줍니다. */

return NavigationView {
  TripListView(presenter: presenter)
}
```

```Swift
/* ContentView에서 아래와 같이 Body부분을 수정하여 Presenter의 Interactor과 모델로 이어지는 데이터 흐름을 보여줍니다. */

TripListView(presenter:
  TripListPresenter(interactor:
    TripListInteractor(model: model)))
```

### Routing 적용하기
→ 화면 전환이 일어날 때, 다음 화면으로 전환할 때 필요한 데이터와 함께 화면을 전환하는 기능을 말합니다. 새로운 View를 생성하고 필요한 데이터를 넘겨줌으로써 다음 View로 이동합니다.

```Swift
/* TripListRouter 파일을 생성하여 다음과 같은 코드를 추가합니다. */

import SwiftUI

class TripListRouter {
  func makeDetailView(for trip: Trip, model: DataModel) -> some View {
    let presenter = TripDetailPresenter(interactor:
      TripDetailInteractor(
        trip: trip,
        model: model,
        mapInfoProvider: RealMapDataProvider()))
    return TripDetailView(presenter: presenter)
  }
}
```

```Swift
/* TripListPresenter에서 라우터를 추가합니다. */

private let router = TripListRouter()
```

```Swift
/* TripListPresenter에서 라우터를 추가합니다. */

func linkBuilder<Content: View>(
    for trip: Trip,
    @ViewBuilder content: () -> Content
  ) -> some View {
    NavigationLink(
      destination: router.makeDetailView(
        for: trip,
        model: interactor.model)) {
          content()
    }
}
```

```Swift
/* TripListView에서 다음과 같이 코드를 변경합니다. */

self.presenter.linkBuilder(for: item) {
  TripListCell(trip: item)
    .frame(height: 240)
}
```

→ 위의 방법으로 Presenter는 Router를 사용해 다음 View를 생성하고, 해당 View를 보여주기 위한 NavigationLink를 생성한 후에, View는 NavigationLink를 사용해 다음 View로 이동하게 되며, 이 때 Router는 필요한 데이터를 함께 전달합니다.


### 종합하여...

모듈은 Presenter, Interactor 및 Rotuer에 대한 인터페이스를 노출하고, 각 모듈을 패키징하지 않는 한 모듈을 그룹으로 개념화 할 수 있습니다.

위와 같은 방식으로 TripListView, TripListPresenter, TripListInteractor, TripListRouter를 가져와 TripListModule이라는 그룹으로 그룹화합니다.

Detail의 세부 클래스 역시, 동일한 방식으로 구현할 수 있습니다.

모듈은 코드를 깨끗하고 분리된 상태로 유지하는 좋은 방법이며, 일반적으로 모듈은 개념적 화면/기능이어야 하며 라우터는 사용자를 모듈 간에 전달합니다.

뷰 상태를 확인하고, 엔티티와 직접 상호 작용하는 단일 ObservableObject로 프레젠터 및 인터랙터 기능을 축소하여 적용할 수 있습니다.
