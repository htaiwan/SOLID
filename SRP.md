# SRP

## Agenda

- [定義](#1)
- [定義解讀](#2)
- [優點](#3)
- [代碼實踐](#4)
- [如何實踐](#5)

## Note

<h2 id="1">定義</h2>

> **單一職責原则（Single Responsibility Principle）**
> 
> A class should have a single responsibility, where a responsibility is nothing but a reason to change.

- 一個類別只允許只有一個職責，也就是只有一個導致該類別變更的原因。

<h2 id="2">定義解讀</h2>

- 類別的職責變化也是導致這個類別變更的原因，如果一個類別有多餘的職責，就會有多種原因導致這個類別變化，從而導致這個類別維護變得困難。
- 在軟體開發過程隨著需求不斷的增加，可能會給原本的類別添加一些本來不屬於他的職責，從而違背單一職責原则。如果發現一個類別當前的職責不是只有一個，就應該將原本不屬於該類別的職責分離出去。

<h2 id="3">優點</h2>

- 類別與方法的職責若劃分得很清楚，可以提昇程式碼的可讀性，更實際降低程式出錯的風險。

<h2 id="4">代碼實踐</h2>

### 需求點

- 初始需求: 需要創造一個員工類別，這個類別有員工的一些基本訊息。
- 新需求: 增加兩個方法。
	- 判定員工今年是否有升職。
	- 計算員工的薪水。

### 不好的設計

```obj-c
//================== Employee.h ==================

@interface Employee : NSObject

//============ 初始需求 ============
@property (nonatomic, copy) NSString *name;       //員工姓名
@property (nonatomic, copy) NSString *address;    //員工住址
@property (nonatomic, copy) NSString *employeeID; //員工ID
 
 
 
//============ 新需求 ============
//計算員工的薪水
- (double)calculateSalary;

//判定員工今年是否有升職
- (BOOL)willGetPromotionThisYear;

@end

```

- 新需求的做法看似沒有問題，因為都是和員工有關，但卻違背了單一職責原则。因為這兩個方法並不是員工本身的職責。
- 如果之後晉升機制改變，計算薪水方式改變都要修改此類別。
- calculateSalary：這屬於會計部門，因為薪水的計算是由會計部負責。
- willPromotionThisYear：這屬於人事部門，因為考核或晉升都是由人事部門負責。


### 好的設計

- 保留員工類別的初始設計

```obj-c
//================== Employee.h ==================

@interface Employee : NSObject

//初始需求
@property (nonatomic, copy) NSString *name;
@property (nonatomic, copy) NSString *address;
@property (nonatomic, copy) NSString *employeeID;
```

- 創建新的會計部門

```obj-c
//================== FinancialApartment.h ==================

#import "Employee.h"

//會計部門
@interface FinancialApartment : NSObject

//計算薪水
- (double)calculateSalary:(Employee *)employee;

@end
```

- 創建新的人事部門

```obj-c
//================== HRApartment.h ==================

#import "Employee.h"

//人事部门类
@interface HRApartment : NSObject

//今年是否晋升
- (BOOL)willGetPromotionThisYear:(Employee*)employee;

@end
```

- 透過創建兩個新類別，把兩個職責分離出去，讓本來該處理這些職責的類別來進行處理。

<h2 id="5">如何實踐</h2>

- 在實際的開發過程中，我們很容易會將不同的責任混在一起，這點需要多注意。
