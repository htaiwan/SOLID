# OCP

## Agenda

- [定義](#1)
- [定義解讀](#2)
- [優點](#3)
- [代碼實踐](#4)
- [如何實踐](#5)

## Note

<h2 id="1">定義</h2>

> **開閉原則（Open Close Principle）**
> 
> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification

- 在軟體的基本單元中，如classes, modules, functions應該要傾向易於擴展，但不隨便可以修改。

<h2 id="2">定義解讀</h2>

- 用抽象來架構框架，用實現來擴展細解。
- 不以改動原來類別的方式來實現新需求，而是應該實現原本定義抽象出來的接口。

<h2 id="3">優點</h2>

- 在不改動原有的程式碼的前提下給程式擴展功能，增加程式的可擴展性，同時也降低程式的維護成本。


<h2 id="4">代碼實踐</h2>

### 需求點

- 設計一個線上課程，剛開始的時候，只有文字課程。
- 後來增加的視訊課程，錄音課程和直播課程。

### 不好的設計

- 最開始的文字課程

```obj-c
interface Course : NSObject

@property (nonatomic, copy) NSString *courseTitle;         //課程名稱
@property (nonatomic, copy) NSString *courseIntroduction;  //課程介紹
@property (nonatomic, copy) NSString *teacherName;         //講師姓名
@property (nonatomic, copy) NSString *content;             //課程內容

//新需求：視訊課程
@property (nonatomic, copy) NSString *videoUrl;

//新需求：錄音課程
@property (nonatomic, copy) NSString *audioUrl;

//新需求：直播課程
@property (nonatomic, copy) NSString *liveUrl;

@end

```

- 每次新增需求都在原有的course類別進行修改。
	- 都在原有Course類別，新增新課程所需要的數據。
- 造成每次產生course物件，都會包含不屬於自己的數據。
	- 視訊課程物件並不是單純的視訊課程物件，還包含了錄音跟直播的url。
- 隨著需求的增加，需要反覆修改之前創建的類別，給新增的類別造成不必要多餘的屬性。


### 好的設計

- 首先，在原有的course類別中包含所有課程都含有的數據。

```obj-c
@interface Course : NSObject

@property (nonatomic, copy) NSString *courseTitle;         //課程名稱
@property (nonatomic, copy) NSString *courseIntroduction;  //課程介紹
@property (nonatomic, copy) NSString *teacherName;         //講師姓名

@end
```

- 針對其他課程，文字，視訊，錄音，直播課程都是繼承course類別。並且在繼承後，新增自己該有的屬性。

```obj-c
//================== TextCourse.h ==================

@interface TextCourse : Course

@property (nonatomic, copy) NSString *content;             //文字内容

@end

//================== VideoCourse.h ==================

@interface VideoCourse : Course

@property (nonatomic, copy) NSString *videoUrl;            //視訊url

@end

//================== AudioCourse.h ==================

@interface AudioCourse : Course

@property (nonatomic, copy) NSString *audioUrl;            //音檔url

@end

//================== LiveCourse.h ==================

@interface LiveCourse : Course

@property (nonatomic, copy) NSString *liveUrl;             //直播url

@end
```

- 隨著課程的增加，不需要反覆修改最初的父類(course)，只需要新增一個繼承於他得子類別，並添加該有的屬性或行為。
- 因為各種課程都有獨有的數據都被分散到不同課程的子類別中，所以每個子類別或行為都不會有其他多餘無謂的數據。

<h2 id="5">如何實踐</h2>

- 在設計之初，就要考慮哪些行為或數據是一定不變(或者很難改變)的，哪些是很容易改變。
- 將容易改變抽象的成接口(category)，以便與將來創造具體的實現來對應不同的需求。

