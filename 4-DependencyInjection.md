# DEPENDENCY INJECTION
Farklı class'lar birbirlerine ve başka property'lere bağımlılık (dependency) gösterir. Bu durum, programın değiştirilebilirliğini ve esnekliğini kısıtlar. Bunun önüne geçmek için bağımlılıkların gevşetilmesi (Loose Coupling) yaklaşımı sergilenir.

Dependency injection da bu yaklaşımla bağımlılıkların sınıflara enjekte edilmesine dayanır.

Başka componentler tarafından kullanılması istenen Service'ler, `@Injectable` dekoratörü ile işaretlenir.

Servisler, kullanılacakları Component içinde ya da o Component'in declare edildiği module içinde `providers` kısmına eklenmelidir.

```typescript
import { Component } from '@angular/core';
import { Product, ProductService } from '../services/product.service';

@Component({
  ...
  providers: [ProductService]
})
export class ProductComponent {
              //ProductService'in injectionu
  constructor(private productService: ProductService){
    //Inject edilen ProductService'in Component içinde kullanımı
    console.log(productService.getProducts());
  }

  /*Aynı şekilde inject edilen Service içindeki fonksiyonun
  Component içinde kullanımı.*/
  productsList: Product[] = this.productService.getProducts();
}
```

```html
<ul>
    <li *ngFor="let product of productsList">
      {{product.name}} - {{product.quantity}}
    </li>
</ul>
```

Örnekte verilen constructordaki injection işlemi otomatiktir. Manuel hali ise `@Inject` dekoratörü ile yapılabilir:

```typescript
constructor(@Inject(ProductService)private productService){
    ...
  }
```

## DI (Dependency Injection) Token Türleri
### Default Type Token
```typescript
providers: [ProductService]
```

```typescript
costructor(private productService: ProductService){
    ...
}
```

### Type Token
```typescript
providers: [{provide: ProductService, useClass: ProductService}]
```

```typescript
costructor(private productService: ProductService){
    ...
}
```

### String Token
```typescript
providers: [{provide: "productService", useClass: ProductService}]
```

```typescript
costructor(@Inject("productService")private productService: ProductService){
    ...
}
```

### Injection Token
Eğer aynı isimde tanımlanmış birden fazla String Token olursa, son tanımlanan token öncekileri ezecektir. Bunun önüne geçmek için Injection Token kullanılabilir.

```typescript
//injection-token.ts dosyası...
import { InjectionToken } from "@angular/core";

export const productServiceIT : InjectionToken<any> = new InjectionToken("");
```

```typescript
providers: [{provide: productServiceIT, useClass: ProductService}]
```

```typescript
costructor(@Inject(productServiceIT)private productService: ProductService){
    ...
}
```

## Provider Türleri
### Class Provider - useClass
Tür/sınıf provide edilecekse `useClass` kullanılır.

```typescript
providers: [
  {
    provide: ProductService, 
    useClass: ProductService
  }
  ]
```

### Value Provider - useValue
Basit/metinsel değerler provide edilecekse `useValue` kullanılır.

```typescript
providers: [
  {
    provide: 'URL', 
    useValue: 'https://www.google.com'
  }
  ]
```

Value provider'da fonksiyon da provide edilebilir.

```typescript
providers: [
  {
    provide: 'URL', 
    useValue: () => {
      return "https://www.google.com";
    }
  }
  ]
```

### Factory Provider - useFactory
Provide edilecek servisi yapılandırırken herhangi bir dış kaynaktan konfigürasyonel 
değer alınacaksa, farklı bir ihtiyaçtan kaynaklı bir API'a bağlanılacaksa veya 
provide edilecek nesne belirli şartlar altında farklı davranışlar sergileyecekse 
bu ihtiyaç `useFactory` ile gerçekleştirilebilir.

```typescript
providers: [
    //ProductService'den önce HttpClient ile API'a requestte bulunduktan sonra ProductService kullanımı için senaryoda;
    {
      provide: "productService", useFactory: (httpClient: HttpClient) => {

        const obs = httpClient.get("https://jsonplaceholder.typicode.com/posts").
          subscribe({ next: data => console.log(data) });
        return new ProductService(); //instance olarak döndürmeliyiz

      }, deps: [HttpClient]
    }
  ]
```

* useValue'da tanımlanmış olan fonksiyon inject edilen yere gönderilir; useFactory'de ise tanımlanmış olan fonksiyon handle edilir, result'ı inject edilen yere gönderilir.

### Aliased Class Provider - useExisting
Provide edilmiş bir servisi farklı bir referansla temsil etmek istenen durumlarda `useExisting` kullanılır.

```typescript
providers: [
  A,
  {provide: B, useClass: A}
  {provide: C, useExisting: A}
  ]
```

```typescript
constructor (private a: A)  //İlk provider için instance oluşturur.
```
```typescript
constructor (private b: B)  //İkinci provider için instance oluşturur.
```
```typescript
constructor (private c: C)  //c parametresine ilk provider'ın instance'ı inject edilir.
```




# SERVICE
Servisler, belirli bir işlevi yerine getiren sınıflardır. Bu servisler Component'ler tarafından kullanılır, böylece Component'ler iş süreçlerinden arındırılmış olur.

Servisler,

* İşlem gerçekleştirilirken kullanılabilir
* Uygulamanın genel state'ini tutabilir.
* Herhangi bir dış servis veya API'dan veri alabilir.
* Component'ler arasında veri iletişimi sağlayabilir.

Angular CLI'da `ng g s serviceName` komutu ile servis oluşturulabilir.

