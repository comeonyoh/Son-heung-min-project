# Son Heung-min project

<aside>
💡 - Son Heung-min is the fastest and most reliable football player in South Korea.
- I wanted to develop a repetitive List UI screen as fast and efficiently as Son Heung-min.

</aside>

### Effective engineer

$$
Leverage = {Impact\;Produced \over Time\;invested}
$$

- [Effective Engineer](https://www.yes24.com/Product/Goods/110243880)
- [http://www.effectiveengineer.com](http://www.effectiveengineer.com/)

### Motivation

- The majority of an iOS developer’s work involves rendering screens through the UICollectionView.
    - Examples
        
        ![IMG_1125.PNG.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/IMG_1125.PNG.png)
        
        ![IMG_1126.PNG.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/IMG_1126.PNG.png)
        
- How should develop to achieve high leverage?
- Strategy Map
    
    ![Screenshot 2023-11-10 at 11.39.53 AM.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/Screenshot_2023-11-10_at_11.39.53_AM.png)
    
    - From the user’s view, Let’s define only **deltas** only to **input** and **output**.
    - From the module’s view, establish **common rules** and only **open up parts** that require customization.

### A point of `Output`

---

- The part that should be displayed to the user.
- Most iOS Developers invest a lot of time and energy into drawing this. In a very very complex way.
- Main components
    1. **Cell**
        - A single data view.
    2. **Layout**
        - Defines the visual arrangement of the content.
        - eg. Vertical, Horizontal, How many items visible in a row…
    3. **Section**
        - Compose with a single layout and multiple cells.
        - Multiple sections make up a grid(collection) view.
- Conclusion
    - We have to define a **section** which has a single **layout** and **cell’s definition.**
    - We have to define a **UICollectionView** which has a lot of sections.
- Programmatic expression
    
    ```swift
    typealias LayoutBuilder = (_ environment: NSCollectionLayoutEnvironment) -> NSCollectionLayoutSection
    
    // Define Layout
    let layout: CollectionViewSectionLayout.LayoutBuilder = { environment in
    	// have to return a NSCollectionLayoutSection 
    	return section
    }
    
    open class MPSCollectionViewCell: UICollectionViewCell, MPSCollectionViewUIComponent {
    
    	open func update(item: Any) {
            
            if let data = item as? MPSCollectionViewData {
                self.textLabel?.text = data.textLabelValue
                self.imageView?.image = data.imageViewValue
                self.secondaryTextLabel?.text = data.secondaryTextLabelValue
            }
        }
    }
    
    // Define View
    let **퀵_버튼_아이템_View: MPSCollectionViewCell!**
    
    // Define Section
    let **sectionA** = CollectionViewSection<T>(input: 퀵_버튼_Data_Source, view: **퀵_버튼_아이템_View**,  layout: **layout**)
    
    // reuse the layout.
    let **sectionB** = CollectionViewSection<T>(input: 자주_산_상품_Data_Source, view: **자주_산_상품_아이템_View**,  layout: **layout**)
    
    // make a queue
    queue = ...init(requests: [**sectionA**, **sectionB**], policy = .ignore)
    ```
    

### A point of `Input`

---

- Source of Data
    1. Memory/Database
        - A set of **model**
            
            ```swift
            widgets = [
            	  .init(id: , name: ...),
            		.init(id: , name: ...),
            		.init(id: , name: ...)
            ]
            ```
            
    2. API base
        - **URL**
            - eg. [`https://api-gateway-dev.qanda.ai/interests/topics/`](https://api-gateway-dev.qanda.ai/interests/topics/)
        - **Keypath,** Parsing **Model**
            - Example 1
                
                ```json
                {
                  "collection": {
                	  "dt" : "",
                    "users": [
                      {
                        "id": 123,
                        "name": "John Doe",
                        "email": "johndoe@example.com"
                      },
                      {
                        "id": 124,
                        "name": "Jane Smith",
                        "email": "janesmith@example.com"
                      },
                      {
                        "id": 125,
                        "name": "Alice Johnson",
                        "email": "alicejohnson@example.com"
                      }
                    ]
                  }
                }
                
                **// Keypath: collection.users
                // Parsing Model: User → [User]**
                ```
                
            - Example 2
                
                ```json
                
                {
                  "library": {
                    "collection": {
                      "books": [
                        {
                          "title": "Harry Potter",
                          "author": "J.K. Rowling",
                          "publishedYear": 1997
                        },
                        {
                          "title": "The Hobbit",
                          "author": "J.R.R. Tolkien",
                          "publishedYear": 1937
                        },
                        {
                          "title": "1984",
                          "author": "George Orwell",
                          "publishedYear": 1949
                        }
                      ]
                    }
                  }
                }
                
                **// Keypath: library.collection.books
                // Parsing Model: Book → [Book]**
                ```
                
- Conclusion
    - We need just three things **URL**, **Keypath**, **Model**
- Programmatic expression
    
    ```swift
    let widgets = [
    	  .init(id: , name: ...),
    		.init(id: , name: ...),
    		.init(id: , name: ...)
    ]
    
    let section_template_A = CollectionViewSection<T>(input: [T], view: ..., layout: ...)
    let section_template_B = CollectionViewSection<T>(input: api, view: ..., layout: ...)
    
    // 1. local data or DB base use case.
    let **sectionA** = CollectionViewSection(input: **widgets**, view: 퀵_버튼_아이템_View,  layout: layout)
    
    // 2. api base use case.
    let api = https://www...
    let **sectionB** = CollectionViewSection<T>(input: **api**, view: 자주_산_상품_아이템_View,  layout: layout)
    
    // make a queue
    queue = ...init(requests: [**sectionA**, **sectionB**], policy = .ignore)
    ```
    

### A point of `Loader(Queue)`

---

- Management of the tasks.
    - Normal case
        
        ![Screenshot 2023-11-10 at 1.44.55 PM.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/Screenshot_2023-11-10_at_1.44.55_PM.png)
        
    - Error handling case
        
        !['.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa.png)
        
        ![Screenshot 2023-11-10 at 1.47.03 PM.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/Screenshot_2023-11-10_at_1.47.03_PM.png)
        
    - Applied to UI
        
        ![IMG_1126.png](Son%20Heung-min%20project%2007f33a84a9f046fe9fdce8ef74d642fa/IMG_1126.png)
        
- Error handling policy
    - `ignore`
    - `cancellAll`
    - `custom(request, error) → Bool`
        
        ```swift
        loader = CollectionViewLoader(requests: [section1, section2], sectionSpacing: 20.0)
        loader.exceptionHandlingPolicy = .ignore
        ```
        
- Activation validation
    
    ```swift
    loader.sections = [sectionA, sectionB, sectionC]
    
    // For example, let's assume sectionB will be activated to the specific users.
    open class SectionB<E>: CollectionViewSection<E> where E: CollectionViewItem {
    		
    		override public var isActivated: Bool {
    				
    				if(anySpecificCondition) {
    						return false
    				}
            return true
        }
    }
    
    loader.invalidateActivatedSections()
    ```
    

### Additional considerations

---

- Paging policy
    - In my workspace, there are various paging rules according to the domain.
        
        ```swift
        public struct PagingInfo {
                 
            public enum Policy {
                //  No paging effect. Just a single page.
                case none
                // It manages a token value internally.
                case token
                // It manages a page number internally.
                case page
                // It manages a header's cursor value internally.
                case headerCursor
            }
            
            public var size: Int
            public var policy: Policy
        }
        
        let **sectionA** = CollectionViewSection(input: **widgets**, view: 퀵_버튼_아이템_View,  layout: layout)
        sectionA.paging = .init(policy. page)
        
        //...
        sectionB.paging = .init(policy. headerCursor, size: 20)
        
        //when the section use token policy and it uses a custom key for the token.
        sectionC.paging = .init(policy. token)
        sectionC.paging.customKeyFinder = { object in
        	 return object["next] as? String
        }
        ```
        
- Pull to Refresh
    
    ```swift
    let aViewController = MPSCollectionViewController()
    aViewController.supportRefreshControl = true
    ```
    
- Logging Automation
    
    ```swift
    let widgets: [Widget] = [
    	  .init(id: 7sx2hJ9L0u, name: ...),
    		.init(id: Q8xG5mF1oZ, name: ...),
    		.init(id: 4bV6rN2wXq, name: ...),
    ]
    
    let **sectionA** = CollectionViewSection(input: **widgets**, view: 퀵_버튼_아이템_View,  layout: layout)
    sectionA.identifier = "section_of_widgets"
    
    // When click the cell of the section, this closure method will be called.
    sectionA.handler = { context in
    	
    	/*
    		When the identifier is not empty, click logging will be send automatically.
    		
    		event name: click
    		event parameters: [
    				"index" : context.index,
    				"identifier_of_section" : context.identifier, // section_of_widgets
    				"identifier_of_section_item" : context.item.id, // context.item is Widget type's object
    
    				// also, refer to a ViewController's context.
    		]
    	*/
    	
    	// you can customize the action. 
    	context.pushViewController(viewController, animated: true)
    
    	return false
    }
    ```
    
- Relationship between the `Model` and `View`
    - Relationship
        
        ```swift
        public protocol MPSCollectionViewData: Decodable {
            
            var textLabelValue: String? { get }
            
            var secondaryTextLabelValue: String? { get }
        
            var imageViewValue: String? { get }
        }
        
        open class MPSCollectionViewCell: UICollectionViewCell ,... {
        
        		@IBOutlet public weak var textLabel: UILabel?
            
            @IBOutlet public weak var secondaryTextLabel: UILabel?
            
            @IBOutlet public weak var imageView: UIImageView?
        
        		open func update(item: Any) {
                
                if let data = item as? MPSCollectionViewData {
                    self.textLabel?.text = data.textLabelValue
                    self.secondaryTextLabel?.text = data.secondaryTextLabelValue
                    self.imageView?.image = // ... Image(data.imageViewValue)
                }
            }
        }
        
        // In the internal code...
        dataSource = UICollectionViewDiffableDataSource(collectionView: collectionView, cellProvider: { [weak self] collectionView, indexPath, itemIdentifier in
        
        		let cell = collectionView.dequeueReusableCell(withReuseIdentifier: identifier, for: indexPath) as! MPSCollectionViewCell
        
        		// ...
        		cell.update(item: data)
        		// ...
        
        		return cell
        })
        ```
        
    - When use the custom `Model`  and `View`
        
        ```swift
        // **[MODEL]**
        // Just confirm to the specific protocol and define appropriate property value.
        private struct Widget: Hashable, MPSCollectionViewData {
        
            var id, name, imageURL: String
            
            var textLabelValue: String? { name }
        		var imageViewValue: UIImage? { imageURL }
        }
        
        // **[VIEW]**
        // By default, the cell has nothing to do.
        // Simply modify the auto-layout constraints in the xib or storyboard file.
        ```
        

### Pros and Cons

---

- Pros
    - We can save energy and time by writing too many codes. Productivity.
    - Reduce compile time.
    - Consistency.
- Cons
    - Complex loading steps.
    - Customizing.