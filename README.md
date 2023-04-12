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
→ Configurator는 VIP 주기의 구성 요소를 인스턴스화 하고, 연결하여 단방향 사이클을 생성합니다.

→ 모든 Scene에서 하나의 Configurator를 한번만 호출하여 사용해야 합니다.

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


