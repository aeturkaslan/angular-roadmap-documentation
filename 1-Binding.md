# BINDING
Angular'da **binding (bağlama)**, component'in template'i ile veri arasında dinamik bir bağlantı oluşturur.

## Interpolation
Çift süslü parantez `{{ }}` içine girilen değerler, direkt olarak template sayfasında görüntülenir.

```typescript
//app.component.ts dosyası
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'First Angular Application';
  name = 'Ahmet';
}
```

```html
<!--app.component.html dosyası-->
<h1>First Angular App</h1>
<p>{{title}}</p>
<p>{{name}}</p>
```

app.component.ts dosyasındaki değişkenler, interpolation ile dinamik olarak html template sayfasına aktarılır. Değişkenlerde yapılan değişiklikler template sayfasında da uygulanarak gösterilir.



## Property Binding (Özellik Bağlama)
Template'de kullanılan elementlerin özelliklerini component içindeki değere bağlayarak element özelliklerinin dinamik olarak değiştirilebilmesini sağlar.

```html
 <input [value]="name">
```
Örnekteki kullanım, sayfadaki input elementinin component'teki name değişkeni ile doldurulmasını sağlar.
<hr>
Aynı zamanda directive özellikleri ile de bağlanabilir.

```html
<img [ngSrc]="profilePhotoUrl" alt="The current user's profile photo">
```
Örnekte resim kaynağını belirleyen ngSrc özelliği profilePhotoUrl değeri ile bağlanmıştır ve bu sayede resim değişikliği durumunda component içindeki değişken değerindeki değişiklik, yeni resmin sayfaya yansımasında yeterli olacaktır.

<hr>

Property (özellik) içinde interpolation kullanılabilir.

```html
<img [ngSrc]="profilePhotoUrl" alt="{{ name }}'s profile photo">
```



## Class and Style Binding (Sınıf ve Stil Bağlama)
Template HTML sayfasında kullanılan bir elementin class ve style'larını dinamik olarak değiştirmek için kullanılır.

- Bir elementin class'ını dinamik olarak değiştirmek için
`[class.className]="expression"` synthaxı kullanılır.

```html
 <button [class.active]="isActive">Click Me</button>
```
Örnekte buton elementinin aktifliğini belirleyen 'active' classına sahip olup olmayacağını, component içindeki isActive değişkeni belirler.

<hr>

- Bir elementin stilini dinamik olarak değiştirmek için 
`[style.styleName]="expression"` synthaxı kullanılır.

```typescript
export class AppComponent {
  isHighlighted: boolean = true;
  fontSize: string = '20px';
}
```

```html
 <p [style.background-color]="isHighlighted ? 'yellow' : 'transparent'" 
   [style.font-size]="fontSize">
   Highlighted text
</p>
```
Örnekte HTML sayfasında kullanılan paragraf elementinin stilleri, Component içinde tanımlı değişkenlere göre belirlenmektedir. Değişkenlerin şu anki değerine göre paragraf, 20px yazı boyutu ve yeşil arkaplan rengine sahip olur. Component'te değişiklik yapılması halinde elementin stili de buna göre dinamik olarak değişir.




## Event Binding (Olay Bağlama)
Event binding, DOM olaylarının Component'e bağlanmasını sağlar. Kullanıcının yaptığı fareyle butona tıklama, klavyeden bir tuşa basma gibi işlemlerle Component içindeki fonksiyonlar tetiklenmiş olur.

Genel synthaxı `(eventName)="methodName()"` şeklindedir.

### Click Event

```typescript
export class AppComponent {
  onClick() {
    alert('Butona tıklandı!');
  }
}
```

```html
<button (click)="onClick()">Tıklayınız</button>
```
Örnekte, kullanıcı butona tıkladığında Component içindeki onClick methodu tetiklenir ve çalıştırılır.




## Two-way Binding (Çift Yönlü Bağlama)
Two-way binding'de `[(ngModel)]` ile yapılan bağlantı sayesinde sayfa üzerinde yapılan değişiklikler, doğrudan değişken üzerine yansır. Bu değişikliği kullandığımız interpolation ile anlık olarak gözlemleyebiliriz.

```html
<h1>First Angular App</h1>
<p>{{title}}</p>
<p>{{name}}</p>

<input type="text" [(ngModel)]="name">
```

![alt text](./img/two-way-1.png)

![alt text](./img/two-way-2.png)







