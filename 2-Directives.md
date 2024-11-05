# DIRECTIVES
Angular'da, HTML elementlerine ekstra özellik ve davranış eklemek için kullanılan etiketlerdir.

# BUILT-IN DIRECTIVES

## ngFor Directive
İterasyonel işlemler yapmak için kullanılır.

```typescript
@Component({...})
export class DirectiveComponent {
  names = ["Ali", "Ayşe", "Ahmet", "Fatma"]

}
```

```html
<div>
    <p>ngFor Directive</p>
    <ul>
        <li *ngFor="let name of names">{{name}}</li>
    </ul>
</div>
```

## ngIf Directive
Verilen koşula göre HTML elementinin görünürlüğünü kontrol etmek için kullanılır.

```typescript
@Component({...})
export class DirectiveComponent {
  visible : boolean = true;
}
```

```html
<div>
    <p>ngIf Directive</p>
    <div *ngIf="visible ; else elseContent">This is visible only if "visible" variable is true</div>
    <ng-template #elseContent>This is shown as "elseContent" if the "visible" variable is false </ng-template>
</div>
```
Örnekte, eğer visible true ise div kısmı gösterilir, false ise ng-template kısmı gösterilir


## ngSwitch Directive
Verilen koşula göre HTML elementinin görünürlüğünü kontrol etmek için kullanılır.

```typescript
@Component({...})
export class DirectiveComponent {
  sayi : number = 1;
}
```

```html
<div>
    <p>ngSwitch Directive</p>
    <div [ngSwitch]="sayi">
        <div *ngSwitchCase="1">1</div>
        <div *ngSwitchCase="2">2</div>
        <div *ngSwitchDefault>Diğer</div>
    </div>
</div>
```



## ngClass Directive
HTML elementlerinin class değerlerini dinamik olarak değiştirebilmeye yarar.

```typescript
@Component({
  styles: [".myclass{background-color:red}"]
})
export class DirectiveComponent {
  cl : string = "myclass";
}
```

```html
<div>
    <p>ngClass Directive</p>
    <input type="text" [ngClass]="cl">
</div>
```



## ngStyle Directive
HTML elementlerinin class değerlerini dinamik olarak değiştirebilmeye yarar.

```typescript
@Component({...})
export class DirectiveComponent {
  style : any = {
    'background-color' : 'blue',
    'color' : 'white',
    'font-size' : '12px',
  }
}
```

```html
<div>
    <p>ngStyle Directive</p>
    <input type="text" [ngStyle]="style">
</div>
```

# CUSTOM DIRECTIVES

Direktifler, `@Directive` dekoratörü ile işaretlenmiş olmalıdır.

* Directive selectoru köşeli parantez içinde yazılırsa, bu directive'in attribute olarak kullanılacağı anlamına gelir.

```typescript
import { Component } from '@angular/core';

@Directive({
    selector: '[appExample]'
})
export class ExampleDirective {   
}
```
şeklinde tanımlanan directive selectorünün HTML elemanında kullanımı,

```html
<div appExample>
    Deneme
</div>
```
<hr>

* Directive selectoru '.' ile yazılırsa, bu directive'in class olarak kullanılacağı anlamına gelir.

```typescript
import { Component } from '@angular/core';

@Directive({
    selector: '.appExample'
})
export class ExampleDirective {   
}
```
şeklinde tanımlanan directive selectorünün HTML elemanında kullanımı,

```html
<div class="appExample">
    Deneme
</div>
```

Custom directiveler genelde attribute olarak kullanılır.

## Directive Parametre Tanımlama
Directivelere parametre verebilmek için `@Input` dekoratörü kullanılır.

```typescript
import { Directive, ElementRef, Input, OnInit } from '@angular/core';

declare var $:any; //jquery kullanabilmek için

@Directive({
  selector: '[appExample]'
})
export class ExampleDirective implements OnInit{

  constructor(private element:ElementRef) {
   }

  ngOnInit(): void {
    this.element.nativeElement.style.backgroundColor =this.color;
    $(this.element.nativeElement).fadeOut(2000).fadeIn(); //jquery
  }
  @Input() color:string;
}
```

```html
<div>
    <p>Our Custom appExample Directive</p>
    <p appExample color="red">appExample Directive</p>
</div>
```

## HostListener
Oluşturulan directive'in hangi event ile görevlendirileceğini belirlememizi sağlayan dekoratördür.

```typescript
@Directive({
  selector: '[appExample]'
})
export class ExampleDirective implements OnInit{
  @HostListener("click")
  onClick(){
    alert("HTML nesnesine tıklandı!")
 }
}
```

## HostBinding
Directive'in işaretlediği DOM nesnesinin bir özelliğine bind olarak işlemler gerçekleştirilebilmesini sağlar.

```typescript
@Directive({
  selector: '[appExample]'
})
export class ExampleDirective implements OnInit{
  @HostBinding("style.color")
    writingColor : string = "yellow";
}
```



# STRUCTURAL DIRECTIVES
HTML nesnesini ve DOM yapısını fiziksel olarak manipüle ederek template üzerinde değişiklikler yapan directivelerdir

`*` karakteri ile çağrılırlar.

Structural directivelerde `templateRef` ve `viewContainerRef` kullanılır.

Structural directivelerin davranışları setter propertyler ile belirlenir.

Selector ile setter property'nin ismi aynı olmak **zorundadır.** ve setter property `@Input` dekoratörü ile işaretlenmelidir.

## Custom If Structural Directive

```typescript
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appCustomif]'
})
export class CustomifDirective {

  constructor(private templateRef: TemplateRef<any>, private viewContainerRef: ViewContainerRef) {
    //TemplateRef ve ViewContainerRef parametrelerini inject ettiğimiz için bu directive, structural directive özelliği gösterecektir.
  }

  @Input() set appCustomif(value: boolean) {
    if (value) {
      this.viewContainerRef.createEmbeddedView(this.templateRef); //Eğer true ise, HTML nesnesini DOM nesnesine ekle (Göster)
    }
    else {
      this.viewContainerRef.clear(); //Eğer false ise, HTML nesnesini DOM'da gösterme
    }
  }
}
```

```html
<h2>Custom If Structural Directive</h2>
<div *appCustomif="true">
    Custom If Directive, true değeri ile Gösteriliyor.
</div>
```

## Custom For Structural Directive

```typescript
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appCustomfor]'
})
export class CustomforDirective {

  constructor(private templateRef: TemplateRef<any>, private viewContainerRef: ViewContainerRef) { }

  @Input() set appCustomfor(value: number) {
    for (let i = 0; i < value; i++) {
      this.viewContainerRef.createEmbeddedView(this.templateRef);
    }
  }
}
```

```html
<div *appCustomfor="5">
    <li>Ahmet</li>
</div>
```