# COMPONENT
Componentler, uygulama içerisinde belli görevleri tek başına yapabilen yapılardır. 
Ana uygulama olan App Component, alt componentlere bölünür.

Her component için gerekli özellikler,
1. Componentin nasıl çalışacağı ve kullanılacağı ile ilgili konfigürasyon metadatası sağlayan @Component dekoratörü.
2. Sayfada gösterilecek HTML şablonu.
3. Componentin HTML’de nasıl kullanılacağını belirleyen CSS seçici.
4. Tanımlamaların yapıldığı, kullanıcıdan giriş alma, sunucudan veri çekme gibi davranışların olduğu bir TypeScript sınıfı.

Örnek component yapısı,

```typescript
import { Component } from "@angular/core";

@Component({
    selector: 'movies', // <movies></movies> şeklinde çağırılır.
    templateUrl: 'movies.component.html', // movies etiketi ile çağırılacak sayfa
    styleUrl: './movies.component.css' // kullanılacak stil
})
export class MoviesComponent {
   //Componentin davranışlarının tanımlandığı kısım
}
```

## CLI ile Component Ekleme
Component ekleme işlemi, CLI ile de yapılabilir. Bunun için terminalde `ng g c componentName` çalıştırılır.
* `g` - generate
* `c` - component
* `componentName` - oluşturulacak component ismi











# ANGULAR STYLING
Componentler, CSS stillerini doğrudan içerebilir.

```typescript
@Component({
  selector: 'movies',
  template: `<h2>This is movies component</h2>`,
  styles: [` h2 {
        color: blue;
        }`],
})
export class MoviesComponent { }
```


Ya da harici dosyalarda da kullanılabilir.

```typescript
@Component({
  selector: 'movies',
  templateUrl: `movies.component.html`,
  styleUrl: 'movies.component.css'
})
export class MoviesComponent { }
```


## Stil Kapsamı (Style Scope)
Her component, stil kapsamını belirleyen view encapsulation (kapsüllemeyi görüntüle) özelliğine sahiptir. 3 farklı kapsülleme modu vardır: `Emulated` , `ShadowDom` , `None`. Bu modlar @Component dekoratöründe belirlenip değiştirilebilir.


```typescript
@Component({
  ...,
  encapsulation: ViewEncapsulation.None,
})
export class MoviesComponent { }
```

### ViewEncapsulation.Emulated
Angular'da varsayılan kapsülleme, `Emulated` kapsüllemedir. Belirlenen stil, sadece component'in template'inde tanımlı elementlere uygulanır. Böylece bahsedilen component stilinin, başka componentleri etkilememesi sağlanmış olur. Fakat bu component dışında tanımlanmış global stiller yine de Emulated kapsüllenmiş component içindeki elementleri etkileyebilir.


### ViewEncapsulation.ShadowDom
Bu mod Shadow DOM API kullanarak stilin sadece component içini kapsamasını sağlar. Bu mod etkinleştirildiğinde Angular, component'in host elementine bir shadow root ekler, template ve stilleri bu shadow roota oluşturur. Bu kapsülleme modu, **sadece** o component stillerinin component template elementlerini etkilemesini sağlar. Global stiller shadow tree içindeki elementleri, shadow tree içindeki stiller de dışarıdaki elementleri etkileyemez.


### ViewEncapsulation.None
Bu mod, component için tüm kapsüllemeleri devre dışı bırakır. Componentle ilişkili her stil, global stil gibi davranır.

## Template'lerde Stil Tanımlama
Ekstra stil tanımlamaları yapmak için component'in template'inde `<style>` elementi kullanılabilir. Component'in view encapsulation modu her ne ise, bu şekilde tanımlanan stillere de aynen uygulanır.











# COMPONENT LIFECYCLE
Bir component'in yaşam döngüsü (lifecycle) component'in oluşturulması ile yok edilmesi arasındaki süreci ve verdiği tepkileri ifade eder.

## ngOnChanges
Component ayağa kaldırılmadan önce input değişkenlerindeki değişikliklerde tetiklenir.

## ngOnInit
Component ilk oluşturulduğunda (instance'ı oluşturulduğunda değil!) tetiklenir.

## ngDoCheck
Component'in güncellenmesi durumunda sık sık tetiklenir.

## ngAfterContentInit
Component içeriği (template değil, ng-content) ilk kez oluşturulduğunda tetiklenir.

## ngAfterContentChecked
Component'in içeriği güncellendiğinde tetiklenir.

* ngDoCheck, componentteki tüm değişiklikleri kapsar. ng AfterContentChecked ise sadece içerikle ilgili olabilecek değişiklikleri kontrol eder.

## ngAfterViewInit
Component'in view'ı (template) ilk kez oluşturulduğunda tetiklenir.

## ngAfterViewChecked
Component'in view'ı güncellendiğinde tetiklenir.

## ngOnDestroy
Component'in silinmesi durumunda tetiklenir.

```typescript
import { AfterContentChecked, AfterContentInit, AfterViewChecked, AfterViewInit, Component, DoCheck, Input, OnChanges, OnDestroy, OnInit, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-lifecycle',
  template: `Lifecycle Examples <br>
  <input type="text" [(ngModel)]="data"> <br>
  <ng-content></ng-content> <!--ngAfterContentInit tetiklemesi için-->`
})
export class LifecycleComponent implements OnChanges, OnInit, DoCheck, AfterContentInit, 
AfterContentChecked, AfterViewInit, AfterViewChecked, OnDestroy {

  //ngOnChanges
  @Input() data: string;
  ngOnChanges(changes: SimpleChanges): void {
    console.log("1- ngOnChanges Tetiklendi");
  }

  //ngOnInit
  ngOnInit(): void {
    console.log("2- ngOnInit Tetiklendi");
  }

  //ngDoCheck
  ngDoCheck(): void {
    console.log("3- ngDoCheck Tetiklendi");
  }

  //ngAfterContentInit
  ngAfterContentInit(): void {
    console.log("4- ngAfterContentInit Tetiklendi");
  }

  //ngAfterContentChecked
  ngAfterContentChecked(): void {
    console.log("5- ngAfterContentChecked Tetiklendi");
  }

  //ngAfterViewInit
  ngAfterViewInit(): void {
    console.log("6- ngAfterViewInit Tetiklendi");
  }

  //ngAfterViewChecked
  ngAfterViewChecked(): void {
    console.log("7- ngAfterViewChecked Tetiklendi");
  }

  //ngOnDestroy
  ngOnDestroy(): void {
    console.log("8- ngOnDestroy Tetiklendi");
  }
}

```











# @Input - @Output DECORATORS
Input ve Output dekoratörleri, componentler arasında veri aktarımı için kullanılır. Alt component, üst componentten `@Input` dekoratörü ile veri alırken `@Output` dekoratörü ile de veri gönderimi yapar. Output dekoratörü EventEmitter ile beraber kullanılır.

A componenti üst component, B componenti ise alt component olmak üzere, 

```typescript
import { Component } from '@angular/core';

@Component({...})
export class AComponent {
  name:string = "Ahmet"
  lastName2:string="";

  setLastName(event:any){
    this.lastName2=event;
  }
}
```

```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({...})
export class BComponent {
  @Input() name2:string = ""

  lastName:string="";

  @Output() sendToAEvent = new EventEmitter<string>;

  sendToA () {
    this.sendToAEvent.emit(this.lastName);

  }
}
```

```html
<!--A Componentinin template dosyası-->
<h1>A componenti</h1>
<input type="text" [(ngModel)]="name">

<h3 style="color: red;">{{lastName2}}</h3>

<app-b [name2]="name" (sendToAEvent)="setLastName($event)"></app-b>
```

```html
<!--B Componentinin template dosyası-->
<h2>B componenti</h2>
<h3>{{ name2 }}</h3>

<input type="text" [(ngModel)]="lastName">
<button (click)="sendToA()">A componentine gönder</button>
```


