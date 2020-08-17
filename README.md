# bejewel_admin

`2019.09 - 2019.11`

### 프로젝트 구조

**네트워크**
```swift
//
//  AdminAPI.swift
//  AmondzAdmin
//
//  Created by 이규현 on 01/09/2019.
//  Copyright © 2019 bejewel. All rights reserved.
//

import Foundation
import Moya

public enum AdminAPI {
    // 로그인 요청 -> 토큰
    case login(email: String, password: String)
    // ...
    
}

//MARK: - TargetType, Moya에서 제공하는 프로토콜
extension AdminAPI: TargetType {
    
    public var baseURL: URL { ... }
    
    public var path: String { ... }
    
    public var method: Moya.Method { ... }
    
    //MARK: - 테스트하는 동안 API 버전의 가짜객체(mocked/stubbed)를 제공하는데 사용됨
    public var sampleData: Data { ... }
    
    public var task: Task {
        switch self {
        //MARK:- Content-Type | application/json
        case .login(let email, let password):
            return .requestParameters(
                parameters: [
                        PARAMETER.EMAIL.rawValue: email,
                        PARAMETER.PASSWORD.rawValue: password
                ],
                encoding: JSONEncoding.default
            )
        /// ...
    }
    
    public var headers: [String : String]? {
        switch self {
            
        case .login:
            return [
                HEADERKEY.CONTENT_TYPE.rawValue: HEADERVALUE.JSON.rawValue,
                HEADERKEY.ACCEPT.rawValue: HEADERVALUE.JSON.rawValue
            ]
        // ...
    }
}

```

### 사용된 라이브러리

     - 네트워크 처리를 위한 Moya / RxSwift
     
     - 이미지 캐시 처리를 위한 Kingfisher
     
     - 리액티브 프로그래밍을 위한 RxSwift
