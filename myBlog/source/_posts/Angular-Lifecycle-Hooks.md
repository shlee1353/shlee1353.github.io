---
title: '[앵귤러] 라이프사이클 이해하기'
date: 2017-11-15 14:15:26
thumbnail: https://shlee1353.github.io/img/angular.png
tags:
---

## 생명주기(life cycle)
컴포넌트와 지시자는 **생명주기(life cycle)** 를 가집니다. 생명주기란, 컴포넌트나 지시자가 생성되고 파괴될 때까지의 일련의 과정을 말합니다. 생명주기 안에서는 특정 시점에 훅 메서드(hook method)를 호출할 수 있습니다. 예를들어, 컴포넌트와 지시자가 초기화되면 ngOnInit() 훅 메서드가 호출됩니다. 만약 이 시점에 실행할 내용이 있다면 컴포넌트 클래스 내에서 ngOnInit() 훅 메서드를 선언하면 됩니다.
```js
export class MyComponent {
    ngOnInit() { ... }
}
```

## 생명주기 시퀀스
훅 메서드는 호출될 때 일정한 순서로 호출됩니다. 이처럼 일정한 순서로 호출되는 과정을 **생명주기 시퀀스(lifecycle sequence)** 라고 합니다. 아래와 같은 순서로 이루어집니다. 생명주기 시퀀스는 초기화 시점, 상태 체크 시점, 이벤트 시점으로 다시 나뉩니다.
1. ngOnChanges
2. ngOnInit
3. ngDoCheck
---
4. ngAfterContentInit
5. ngAfterContentChecked
6. ngAfterViewInit
7. ngAfterViewChecked
---
8. ngOnDestroy

**초기화**
- ngOnInit() : 컴포넌트나 지시자가 생성될 때 호출되는 메서드. 생명주기의 초기화 시점입니다.
- ngAfterContentInit() : 외부 콘텐츠가 컴포넌트 뷰로 들어갔을 때 호출
- ngAfterViewInit() : 컴포넌트 뷰와 자식 뷰가 초기화되고 나서 호출

**상태체크**
- ngDoCheck() 메서드가 호출되고 나서 ngAfterViewChecked() 가 호출되기까지 콘텐츠와 뷰에 대한 상태 점검이 진행되는 때를 말합니다. 
- **콘텐츠** : 컴포넌트가 선언될 때 내부 엘리먼트에 선언한 엘리먼트의 속성값을 말합니다.
```js
<parent [prop]="..">
    <child name=""></child> // 콘텐츠
</parent>
```
- **Content projection(transclusion)** : 컴포넌트 외부에서 HTML을 가져와 컴포넌트 내부에 원하는 위치에 추가함. Content projection을 의도하지 않았다면 콘텐츠를 컴포넌트 사이에 절대 넣지 마세요.
```js
// AfterContentParentComponent
`<after-content>
   <my-child></my-child>
 </after-content>`

// AfterContentComponent
template: `
  <div>-- projected content begins --</div>
    <ng-content></ng-content>
  <div>-- projected content ends --</div>`
```
- **뷰** : 컴포넌트 내부 탬플릿에 선언한 지시자의 상태
```js
@Component({
    selector: 'parent',
    template:`
    <my-click id="1" *ngIf="status"></my-click>  // 지시자
    <my-click id="2" *ngIf="status"></my-click>`
})
```

**이벤트 시점**
- 컴포넌트가 초기화될 때는 ngOnInit() 메서드가 먼저 호출되지만 컴포넌트나 지시자에서 속성 바인딩에 의한 이벤트가 발생하면 ngOnChanges() 가 호출되고 나서 ngOnInit() 이 호출됩니다. ngOnChanges() 메서드는 현재 상태값(currentValue)과 이전 상태값(previousValue)을 확인할 수 있는 SimpleChanges 객체를 받습니다.
```js
// SimpleChanges 사용법
import {Component, OnChanges, SimpleChanges } from '@angular/core';
ngOnChanges(changes: SimpleChanges) {
    changes.prop
}
```
- ngOnDestroy()는 컴포넌트나 지시자를 제거할 때 실행됩니다.

## 컴포넌트 생명주기
```
cmp-lifecycle
    ㄴ child-cmp.component.ts
    ㄴ cmp-lifecycle.component.ts
    ㄴ my-cmp.component.ts
    ㄴ window.directive.ts
```
```js
// child-cmp.component.ts
import { Component, Input, ContentChild, ViewChild, OnChanges, DoCheck, OnInit, AfterContentInit, AfterContentChecked, AfterViewInit, AfterViewChecked, OnDestroy } from '@angular/core';
import { MyCmp } from './my-cmp.component';
import { Window } from './window.directive';

@Component({
    selector: 'child-cmp',
    template: `<input type="text" [(ngModel)]="message" placeholder="바인딩 있음"> <input type="text" placeholder="바인딩 없음">

    // window 지시자는 ngif 상태에 따라 상태값을 결정
    <window id="1" *ngIf="shouldShow"></window>
    <window id="2" *ngIf="!shouldShow"></window>

    <button (click)="toggle()">View 상태변경</button>`
})

// implements 키워드를 이용해 컴포넌트 클래스에 훅을 구현
export class ChildCmp implements OnChanges, DoCheck, OnInit, AfterContentInit, AfterContentChecked, AfterViewInit, AfterViewChecked, OnDestroy {
    message: string = "";
    oldMessage: string = "";

    constructor() { console.log("\n1-컴포넌트 : constructor()"); }

    @Input()
    set prop(name: string) {
        console.log("@Input prop() 세터 메서드 호출");
    }

    ngOnChanges() { console.log("2--컴포넌트 : ngOnChanges()"); }
    ngOnInit() { console.log("3---컴포넌트 : ngOnInit()"); }
    ngDoCheck() {
        console.log("4----컴포넌트 : ngDoCheck()");
        if (this.message != this.oldMessage) {
            console.log("4----컴포넌트 : ngDoCheck()에서 변화감지");
            this.oldMessage = this.message;
        }
    }

    // @ContentChild 장식자를 이용해 child-cmp의 자식 엘리먼트인 my-cmp 컴포넌트에 선언된 속성값을 받음
    @ContentChild(MyCmp) myCmp: MyCmp;
    oldMyCmpId: any;

    ngAfterContentInit() {
        console.log("5-----컴포넌트 : ngAfterContentInit()");
        this.oldMyCmpId = this.myCmp.value;
        
    }
    ngAfterContentChecked() {
        console.log("6------컴포넌트 : ngAfterContentChecked()");
        
        if (this.oldMyCmpId != this.myCmp.value) {
            console.log("6------컴포넌트 : ngAfterContentChecked()에서 변화감지");
            this.oldMyCmpId = this.myCmp.value;
        }
    }

    shouldShow = true;
    toggle() { this.shouldShow = !this.shouldShow; }

    // Query for a VIEW child of type `Window`
    @ViewChild(Window) window;
    oldWindowId: any;

    ngAfterViewInit() {
        // viewChild is set after the view has been initialized
        console.log("7-------컴포넌트 : ngAfterViewInit()");
        this.oldWindowId = this.window.id;
    }

    ngAfterViewChecked() {
        // viewChild is updated after the view has been checked
        console.log("8--------컴포넌트 : ngAfterViewChecked()");
        if (this.oldWindowId != this.window.id) {
            console.log("8--------컴포넌트 : ngAfterViewChecked()에서 변화감지");
            this.oldWindowId = this.window.id;
        }
    }
    ngOnDestroy() { console.log("9---------컴포넌트 : ngOnDestroy()"); }
}
```
```js
// my-cmp.component.ts
import { Component, Input }  from '@angular/core';

@Component({
    selector: 'my-cmp',
    template: ``
})
export class MyCmp {
    @Input() value: string;
}
```
```js
// cmp-lifecycle.component.ts
// cmp-lifecycle은 my-cmp 컴포넌트를 콘텐츠로 가진다
import { Component } from '@angular/core';

@Component({
    selector: 'cmp-lifecycle',
    template: `
    <button (click)="toggle()">Content 상태변경</button>
    <button (click)="isShow=!isShow">{{isShow==false?'컴포넌트 추가':'컴포넌트 삭제'}}</button>    
    <br>
    <child-cmp *ngIf="isShow" [prop]="shouldShow">
        <my-cmp value="{{shouldShow}}"></my-cmp>
    </child-cmp>`
})
export class CmpLifecycleComponent {
    isShow: boolean = false;

    shouldShow = true;
    toggle() { this.shouldShow = !this.shouldShow; }
}
```
### 콘텐츠 훅 vs. 뷰 훅
콘텐츠 훅과 뷰 훅은 매우 유사하지만 분명한 차이점이 있습니다.
- AfterContent 훅은 ContentChildren 을 고려합니다 (컴포넌트 안으로 투영된 자식 컴포넌트)
- AfterView 훅은 ViewChildren 을 고려합니다 (자식 컴포넌트로 컴포넌트 템플릿 안에 엘리먼트 태그로 존재함)

### 컴포넌트 생명주기 (콘텐츠 훅)
- 초기값을 확인해보면 shouldShow = true, isShow = false 입니다.
- **Content  상태변경** 클릭시, shouldShow = true, false 전환되지만, 외부 뷰의 표시를 결정하지 않기 때문에 훅 메서드를 호출하지 않습니다.
- **컴포넌트 추가** 를 클릭하면, isShow가 true값으로 변하면서 child-cmp가 화면에 그려지기 때문에 초기 출력 로그를 확인할 수 있습니다.
- 컴포넌트 시퀀스 생명주기가 나타나고 (1)-(8), child-cmp 컴포넌트 내부에 ngModel 바인딩이 일어나 (4)-(8) 훅에 대한 로그 결과 출력.
- (4)~(8) 로그가 출력된 이유는 child-cmp 컴포넌트 내부에 ngModel 바인딩이 일어났기 때문입니다.
- 현재까지의 상태값을 확인해보면, shouldShow = true, isShow = true, this.oldMyCmpId = true, this.myCmp.value = true 입니다.
- 이제 다시 **Content  상태변경** 을 클릭하면 아래와같은 출력 로그를 확인할 수 있습니다.
```
@Input prop() 세터 메서드 호출
2--컴포넌트 : ngOnChanges()
4----컴포넌트 : ngDoCheck()
6------컴포넌트 : ngAfterContentChecked()
6------컴포넌트 : ngAfterContentChecked()에서 변화감지
8--------컴포넌트 : ngAfterViewChecked()
```
- **Content 상태변경** 을 클릭할 때 shouldShow 값이 바뀌면서 속성바인딩 [prop]="shouldShow" 의 상태가 변경되면서 이를 @Input() set prop(name: string) {...} 에서 받은 뒤 ngOnChanges() 호출
- child-cmp 엘리먼트의 [prop] 상태가 변경되었기 때문에 상태점검을 히기위해 ngDoCheck() 호출
- my-cmp 엘리먼트의 속성값이 바뀐걸 확인하기 위해, 이전값과 새로운값을 비교한뒤 "ngAfterContentChecked()에서 감지변화" 호출. 이때 my-cmp의 속성값은 **@ContentChild**(MyCmp) myCmp: MyCmp; 로 받아옵니다.

### 컴포넌트 생명주기 (뷰 훅)
- [(ngModel)]="message"로 바인딩 되어있는 input에 텍스트를 추가하면 this.oldMessage, this.message 를 비교하여 "ngDoCheck()에서 변화감지" 를 출력
- 바인딩이없는 input에 입력시 아무런 호출도 일어나지 않습니다.
- **View 상태변경** 클릭시 this.shouldShow 상태값 변경되면서 @ViewChild 장식자를 통해 window 지시자에서 가져오는 id값이 달라집니다.
- 이전 값과 현재 값이 달라져서 "ngAfterViewChecked()에서 변화감지" 호출
- **컴포넌트 삭제** 클릭시 ngOnDestroy() 메서드가 호출됩니다.

## 지시자 생명주기
앞서 살펴본 컴포넌트의 생명주기와 크게 다르지 않습니다.
```js
import { Directive, Input } from '@angular/core';

@Directive({
    selector: '[my-click]',
    host: { '(click)': 'onClick()' }
})
export class MyClickDirective{
    _prop: string = '';

    @Input()
    set prop(name: string) {
        this._prop = name || '';
        console.log("@Input prop() 세터 메서드 호출");
    }
    onClick() { console.log("지시자 : onClick() 메서드 호출"); }
    constructor() { console.log("1-지시자 : constructor()"); }    
    ngOnChanges() { console.log("2--지시자 : ngOnChanges()"); }
    ngOnInit() { console.log("3---지시자 : ngOnInit()"); }
    ngDoCheck() { console.log("4----지시자 : ngDoCheck()"); }    
    ngAfterContentInit() { console.log("5-----지시자 : ngAfterContentInit()"); }
    ngAfterContentChecked() { console.log("6------지시자 : ngAfterContentChecked()"); }
    ngAfterViewInit() { console.log("7-------지시자 : ngAfterViewInit()"); }
    ngAfterViewChecked() { console.log("8--------지시자 : ngAfterViewChecked()"); }
    ngOnDestroy() { console.log("9---------지시자 : ngOnDestroy()"); }
}
```
- **지시자 추가** 를 클릭하면 my-click 지시자가 호출됩니다. prop 속성 바인딩을 통해 자신의 상태를 변경하기 때문에 세터 메서드와 ngOnChanges() 훅이 호출되었습니다.
- 지시자 내부에서 컴포넌트 전용 훅이 호출되지만, 지시자 생명주기로는 사용하지 않습니다. 아래 4가지 공통 훅을 사용합니다.
```js
ngOnChanges(...)
ngOnInit(...)
ngDoCheck(...)
ngOnDestroy(...)
```
- **상태변경** 을 클릭하면 my-click 지시자의 prop 속성값이 변하면서 지시자 내부에서는 상태변경과 검사를 위한 훅 메서드가 호출됩니다.
- **my-click 지시자** 를 클릭하면 onClick() 메서드가 호출 되면서 상태 점검 ngDoCheck() 이 뒤이어 호출됩니다.

## Angular vs. React

|  Angular | React  | 
|---|---|
|  constructor |  constructor() | 
| ngOnChanges | componentWillMount() |
| ngOnInut | render() |
| ngDoCheck | componentDidMount() |
| ngAfterContentInit | componentWillReceiveProps() |
| ngAfterContentChecked | shouldComponentUpdate() |
| ngAfterViewInit | componentWillUpdate() |
| ngAfterViewChecked | render() |
| ngOnDestory | componentDidUpdate() |
| | componentWillUnmount() |