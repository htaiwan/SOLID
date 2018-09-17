# DIP

## Agenda

- [定義](#1)
- [定義解讀](#2)
- [優點](#3)
- [代碼實踐](#4)
- [如何實踐](#5)

## Note

<h2 id="1">定義</h2>

> **依賴倒置原則（Dependency Inversion Principle）**
> 
> - Depend upon Abstractions. Do not depend upon concretions.
> - Abstractions should not depend upon details. Details should depend upon abstractions
> - High-level modules should not depend on low-level modules. Both should depend on abstractions.

- 依賴抽象，而不是依賴實現。
- 抽象不該依賴細節，細節應該依賴抽象。
- 高層模塊不能依賴底層模塊，兩者應該都要依賴抽像。

<h2 id="2">定義解讀</h2>

- 針對接口(category)進行設計，而不是針對實作來設計。
- 盡量不要從具體的類別派生，而是以繼承抽象或者實作接口。
- 關於高層模組跟低層模組的劃分可以按照決策能力的高低進行劃分。
	- 商務層放在高層模組。
	- 邏輯層和數據層放在低層模組。


<h2 id="3">優點</h2>

- 利用抽象來構建基本框架，建立類別之間的相關性，減少類別之間的耦合性。
- 用抽象搭建系統比用具體實現搭建的系統更加穩定，擴展性更高，也便於維護。

<h2 id="4">代碼實踐</h2>

### 需求點

- 前端與後端開發人員共同開發一個項目。

### 不好的設計

- 首先，建立兩個類別(前端與後端)

```obj-c
//================== FrondEndDeveloper.h ==================

@interface FrondEndDeveloper : NSObject

- (void)writeJavaScriptCode;

@end



//================== FrondEndDeveloper.m ==================

@implementation FrondEndDeveloper

- (void)writeJavaScriptCode{
    NSLog(@"Write JavaScript code");
}

@end
```

```obj-c
//================== BackEndDeveloper.h ==================

@interface BackEndDeveloper : NSObject

- (void)writeJavaCode;

@end



//================== BackEndDeveloper.m ==================

@implementation BackEndDeveloper

- (void)writeJavaCode{
    NSLog(@"Write Java code");
}
@end

```

- 接下來，創建一個project類別

```obj-c
//================== Project.h ==================

@interface Project : NSObject

//构造方法，传入开发者的数组
- (instancetype)initWithDevelopers:(NSArray *)developers;

//开始开发
- (void)startDeveloping;

@end



//================== Project.m ==================

#import "Project.h"
#import "FrondEndDeveloper.h"
#import "BackEndDeveloper.h"

@implementation Project
{
    NSArray *_developers;
}


- (instancetype)initWithDevelopers:(NSArray *)developers{
    
    if (self = [super init]) {
        _developers = developers;
    }
    return self;
}



- (void)startDeveloping{
    
    [_developers enumerateObjectsUsingBlock:^(id  _Nonnull developer, NSUInteger idx, BOOL * _Nonnull stop) {
        
        if ([developer isKindOfClass:[FrondEndDeveloper class]]) {
            
            [developer writeJavaScriptCode];
            
        }else if ([developer isKindOfClass:[BackEndDeveloper class]]){
            
            [developer writeJavaCode];
            
        }else{
            //no such developer
        }
    }];
}

@end
```

### 問題點

- 問題1: 假設後台開發語言改成GO語言？
	- 上面的程式碼，有兩個地方需要修改。
	- BackEndDeveloper需要多一個writeGolangCode方法
	- Project中startDeveloping里面需要将BackEndDeveloper的writeJavaCode改成writeGolangCode。

- 問題2: 假設前端開發增加iOS跟android開發？
	- 需要给Project的init裡面需要多導入IOSDeveloper和AndroidDeveloper两个类。
	- 而且按照现有的设计，还要分别向外部提供writeSwiftCode和writeKotlinCode。
	- Project类的startDeveloping方法里面需要再多两个elseif判断，专门判断IOSDeveloper和AndroidDeveloper这两个类。

- **在这两种假设的场景下，高层模块（Project）都依赖了低层模块（BackEndDeveloper）的改动，因此上述设计不符合依赖倒置原则。**

- **正確做法: 将开发者写代码的方法抽象出来，让Project类不再依赖所有低层的开发者类的具体实现，而是依赖抽象。而且从下至上，所有底层的开发者类也都依赖这个抽象，通过实现这个抽象来做自己的任务。**
