# Todoey

## 🎯 Todoey는?
**🗣️ Code-base 기반 UIKit과 `Realm Database`를 이용한 할 일 목록 App 입니다.**

<br>

## ✅ 실행
```
# Clone this project

$ git clone -b develop_Realm <https://github.com/jphong1005/Todoey.git>
```

<br>

## 🎮 데이터 모델 정의
- **Category**
  ```swift
  class Category: Object {
    
    // MARK: - Properties
    /// `Earlier`
    /*
    @objc dynamic var name: String = ""
    @objc dynamic var colour: String = ""
    
    let items: List<Item> = List<Item>()
     */
    
    /// `Later`
    @Persisted(primaryKey: true) var id: ObjectId
    @Persisted var name: String?
    @Persisted var colour: String?
    @Persisted var items: List<Item>    //  1:N 관계
    
    convenience init(name: String, colour: String, items: List<Item>) {
        self.init()
        
        self.name = name
        self.colour = colour
        self.items = List<Item>()
    }
  }
  ```
  
- **Item**
  ```swift
  class Item: Object {
    
    // MARK: - Properties
    @Persisted var title: String = ""
    @Persisted var done: Bool = false
    @Persisted var dateCreated: Date?
    
    var parentCategory: LinkingObjects<Category> = LinkingObjects(fromType: Category.self, property: "items")   //  Relationship (-> Category와 역참조 관계)
  }
  ```

<br>

## 🖨️ Realm Database
|<b>`[CREATE]`</b>|
|:-:|
|<img width="100%" src="https://github.com/jphong1005/Todoey/assets/52193695/e369df7f-cd76-4a9a-acd5-8a24d604fd05">|

|<b>`[READ]`</b>|
|:-:|
|<img width="100%" src="https://github.com/jphong1005/Todoey/assets/52193695/f331cc77-4059-4c2e-b240-aa128912b884">|

|<b>`[UPDATE]`</b>|
|:-:|
|<img width="100%" src="https://github.com/jphong1005/Todoey/assets/52193695/103a18ee-576b-40d8-97b4-621881b7f1ae">|

|<b>`[DELETE]`</b>|
|:-:|
|<img width="100%" src="https://github.com/jphong1005/Todoey/assets/52193695/72f30fe3-4efe-44f7-adbb-d8f2c740d24e">|

<br>

## 🎉 알게된 점
### **Core Data**와 **Realm DB**의 차이 이해
  - **Core Data**
    - `Persistence`는 영속적 데이터를 위해 사용되는 Core Data의 일종의 기능이지 **'그 자체로는 Database가 될 수 없다.'** ❌
    - `In-Memory` 방식으로 앱이 메모리에 로드되고 메모리 내에서 데이터를 유지하면서 saveContext()를 직접적으로 호출해주어야만 `On-Disk에 저장`이되어 영속성 역할을 수행
    - **SQLite 기반**

  - **Realm DB**
    - 플랫폼 자체가 데이터베이스 서버의 역할과 같이 완전히 **분리된 데이터베이스** ex) SQLite, Oracle SQL, Realm 등
    - **NoSQL 기반 DB**로 엔티티 간의 Join을 통한 Relation은 불가능하지만, `LinkingObjects (레퍼런스 또는 링크)`를 통해 엔티티의 역참조 관계를 가져갈 수 있다.
    - 어트리뷰트 정의 시 사용되었던 `@objc dynamic (= Dynamic Dispatch)`키워드의 이해 (Realm 공식 트위터를 통해 현재는 [`@Persisted`](https://twitter.com/realm/status/1414633070683115527)로 대체)
      - **Transaction에 의해 Realm DB의 데이터 모델이 변경되는 과정은 [`Objective-C의 런타임 시점에 결정`](https://academy.realm.io/posts/mobilization-roy-marmelstein-objective-c-runtime-swift-dynamic/)이되고 Objective-C는 동적 디스패치 (= Dynamic Dispatch)를 사용하는 언어이기 때문**
