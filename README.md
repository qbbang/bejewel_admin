# bejewel_admin

`2019.09 - 2019.11`

### 프로젝트 구조

**네트워크** : Moya를 통해 열거형으로 나열한 클래스 "AdminAPI"

ViewModel에서 각각 상속 받는 BaseViewModel를 생성하여 공통 상태코드에 대한 처리를 정의


```swift
open class BaseViewModel {
    
    deinit {
        disposeBag = DisposeBag()
    }
    
    var disposeBag = DisposeBag()
    let provider = MoyaProvider<AdminAPI>()
    
    
    /**
     상태코드에 대한 Alret, Dismiss 처리
     
     - Parameter statue: API 응답 코드.
     - Parameter msg: API 응답 메시지.
     - Parameter vc: API를 호출한 ViewController.
     - Author: 이 규 현
     */
    func alretForStustsCode(status: Int, msg: String, vc: UIViewController) {
        switch status {
        case STATUSCODE.SUCCESS.rawValue:
        case STATUSCODE.FAILER.rawValue:
        case STATUSCODE.INVALID_TOKEN.rawValue:
        case STATUSCODE.EXPIRED_TOKEN.rawValue:
        case STATUSCODE.NO_PARAMETER.rawValue:            
        /// ...
        }
    }
}
```

**ViewModel** : 옵저버블의 데이터가 최종적으로 반영되는 아웃풋 속성들을 정의하고, 

Moya를 통해 들어온 옵저버블 응답 데이터를 처리하는 방을 정의함

```swift
class ProductListViewModel: BaseViewModel {
    
    //MARK:- Outputs
    let productList = BehaviorSubject<[ProductList]>(value: [])
    let reload = BehaviorSubject<Void>(value: ())
    
    var idx = 0
    var page = 0
    private var oldPage = 0
    
    /**
     상품리스트 API를 요청하여 바인딩 함.
     
     정상 응답 시 ProductList와 reload 옵져버블에 이벤트를 발생시킴
     - Parameter idx: 0 - 승인대기, 1 - 재승인요청 , 2 - 승인거절
     - Author :     이규현
     */
    func reqeustProductList(_ idx: Int) {
        print("🚼 reqeustProductList: ", idx)
        provider.rx
            .request(.productList(type: idx))
            .filterSuccessfulStatusCodes()
            .map(ResProductListModel.self)
            .subscribe(onSuccess: { [weak self] (res) in
                self?.page = 0
                self?.page = res.data.productList.count
                self?.productList.onNext(res.data.productList)
                self?.reload.onNext(())
                
            }) { (err) in
                // ..
            }
            .disposed(by: disposeBag)
        
    }
    
    /**
     페이지네이션이 되어 있어 추가 상품리스트를 요청함.
     
     10개가 모두 들어온 경우에만
     - Author: 이 규 현
     */
    func requestAddProductList() {
        if (page % 10 == 0) {
            provider.rx
                .request(.productAddList(type: self.idx, page: self.page))
                .filterSuccessfulStatusCodes()
                .map(ResProductListModel.self)
                .subscribe(onSuccess: { [weak self] (res) in
                    do {
                    // ...
                    } catch {
                        print(error)
                    }
                }) { (err) in
                    // ...
                }
                .disposed(by: disposeBag)
        }
    }
}

```


### 사용된 라이브러리

     - 네트워크 처리를 위한 Moya / RxSwift
     
     - 이미지 캐시 처리를 위한 Kingfisher
     
     - 리액티브 프로그래밍을 위한 RxSwift
