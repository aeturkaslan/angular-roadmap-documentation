# ROUTING
Componentler arası geçiş yapabilmek için routing kullanılır.

## Angular Router'ın Yapılandırılması
1. index.html içinde base URL tanımlanmalıdır.


```html
<!doctype html>
<html lang="en">
<head>
  ...
  <base href="/">
  ...
</head>
```

<br>

2. app-routing dosyası içindeki Routes dizisine, componentlerin rotası tanımlanmalıdır.

```typescript
const routes: Routes = [
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent }
];
```

<br>

3. İstenilen noktada routerLink direktifi ile rotalara bağlantılar oluşturulmalıdır.

```html
<a routerLink="/home">Home</a> | 
<a routerLink="/about">About</a>
<router-outlet></router-outlet>
```

### routerLinkActive

```html
<a routerLinkActive="active" routerLink="/home">Home</a> | 
<a routerLinkActive="active" routerLink="/about">About</a>
<router-outlet></router-outlet>
```

```css
.active{ 
    color: red;
}
```

## Varsayılan rota ayarlama

```typescript
const routes: Routes = [
  { path: "", redirectTo: "/home", pathMatch: "full" },
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent }
];
```

## Wildcard Route

```typescript
const routes: Routes = [
  { path: "", redirectTo: "/home", pathMatch: "full" },
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent },

  { path: "**", component: ErrorComponent }

];
```

## Route Parameter
```typescript
const routes: Routes = [

  { path: "product/:id", component: ProductComponent },

];
```

## Child Routes/Nested Routes
`ProductComponent` ve onun child'ı `ProductDetailComponen`t olmak üzere 2 componentimiz olduğu senaryoda,

```typescript
const routes: Routes = [
  
  { path: "products", component: ProductComponent,
    children: [
    { path: "detail/:id", component: ProductDetailComponent }]
  },
];
```

* `http://localhost:4200/products/detail/3`

Child route altına da child route tanımlanabilir. Örneğin `ProductDetailComponent`'in de `ProductSpecComponent` ve `ProductOverviewComponent` olmak üzere iki child componenti olsun.

```typescript
const routes: Routes = [
  
  { path: "products", component: ProductComponent,
    children: [
    { path: "detail/:id", component: ProductDetailComponent,
        children: [{
        { path: "", redirectTo: "overview", pathMatch: "full" },
        { path: "overview", component: ProductOverviewComponent },
        { path: "spec", component: ProductSpecComponent }
        }]
     }]
  },
];
```

## Guards
Route'lara erişim izni vermek/reddetmek için kullanılan mekanizmalardır. Guard'lar önceden interfaceler ile oluşturulurken, artık fonksiyonel şekilde oluşturulup kullanılabilmektedir.

### CanActivate
Kullanıcının erişmek istediği route'un ya da
component'in kullanılıp kullanılamayacağını belirleyen yapıdır.

```typescript
import { ActivatedRouteSnapshot, CanActivateFn, RouterStateSnapshot } from "@angular/router";

//CanActivate Guard
export const canActivateGuard: CanActivateFn = (route: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
    //...
    //...
    console.log("canActivate Guard");
    return true;
};
```

<hr>

### CanActivateChild
Bu guard, child route'lara erişimin olup olmayacağını belirler. 
CanActivate parent routelarda çalışırken CanActivateChild child routelarda çalışır.

```typescript
import { ActivatedRouteSnapshot, CanActivateChildFn, RouterStateSnapshot } from "@angular/router";

//CanActivateChild Guard
export const canActivateChildGuard: CanActivateChildFn = (childRoute: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
    //...
    //...
    console.log("canActivateChildGuard");
    return true;
};
```

<hr>

### Resolve
Bazı görevler tamamlanmadan route'un etkinleştirilmesini geciktirir.
Örneğin bir API'dan veri çekilmesi gerekiyorsa, onu yapmadan sayfayı yüklemez.

```typescript
//guards.ts dosyası

import { HttpClient } from "@angular/common/http";
import { inject } from "@angular/core";
import { ActivatedRouteSnapshot, ResolveFn, RouterStateSnapshot } from "@angular/router";

//Resolve Guard
export const resolveGuard: ResolveFn<any> = (route: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
    /*ResolveFn'de, yapılacak HTTP isteğinin modellenmesi gerekir. Bunun için httpClient instance'ına ihtiyacımız var. 
    Provider'dan talep etmemiz gerekiyor. Bir fonksiyonda provider'dan Dependency Injection ile instance talebi yapmak için inject
    fonksiyonunu kullanmamız gerekiyor. */

    const httpClient = inject(HttpClient);
    return httpClient.get("https://jsonplaceholder.typicode.com/photos"); //Observable döndürülmesi lazım.(subscribe olmamak gerekiyor.)
};
```

```typescript
//products.component.ts dosyası

import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-products',
  template: `
  <ul>
    <li *ngFor="let photo of photos">
      <a routerLink="[photo.id]">{{ photo.url }}</a>
    </li>
  </ul>
  `
})
export class ProductsComponent implements OnInit {

  constructor(private activatedRoute: ActivatedRoute) { }

  photos;

  ngOnInit(): void {
    this.activatedRoute.data.subscribe(data => this.photos = data["photos"]);
  }
}
```

* Resolve Guard sayesinde Products'a tıklandığında API'dan alınması gereken veri alınmadan, Products sayfası yüklenmez. Veriler alındıktan sonra Products sayfası açılır.

<hr>

### CanMatch
Aynı rotaya farklı koşullarda erişim sağlamak için kullanılır. Örneğin ana sayfanın görüntüsünün, giriş yapılmış durumda ve giriş yapılmamış durumda farklı olmasını sağlar.

```typescript
import { CanMatchFn, Route, UrlSegment } from "@angular/router";

//CanMatch Guard
export const isAdminGuard: CanMatchFn = (route: Route, segments: UrlSegment[]) => {
    return !!localStorage.getItem("admin"); //localstorageda admin varsa true döndürür.
}

export const isUserGuard: CanMatchFn = (route: Route, segments: UrlSegment[]) => {
    return !localStorage.getItem("admin"); //localstorageda admin yoksa true döndürür.
}
```

<hr>

### CanDeactivate 
Kullanıcının bulunduğu componentten ayrılıp ayrılamayacağını belirler.
Genellikle kullanıcının veri girişi yaptığı sayfalarda kaydedilmemiş veriler olduğunda sayfanın terk edilip verilerin kaybedilmesini engellemek için 
kullanılır.

```typescript
import { ActivatedRouteSnapshot, CanDeactivateFn, RouterStateSnapshot } from "@angular/router";

//CanDeactivate Guard
export const canDeactivateGuard: CanDeactivateFn<any> = (component: any, currentRoute: ActivatedRouteSnapshot, 
                                                        currentState: RouterStateSnapshot, nextState: RouterStateSnapshot) => {
                                                            console.log("canDeactivate Guard");
                                                            return true;
                                                            };
```

<hr>

### Using Guards in Routes

```typescript
//app-routing.module.ts dosyası

const routes: Routes = [
  ...
  {
    path: "products", component: ProductsComponent,
    canActivate: [canActivateGuard],
    canActivateChild: [canActivateChildGuard],
    canDeactivate: [canDeactivateGuard],
    resolve: { photos: resolveGuard },
  },
  { path: "dashboard", component: AdminComponent, canMatch: [isAdminGuard] },
  { path: "dashboard", component: UserComponent, canMatch: [isUserGuard] },
  ...
```

<hr>

### Guard'ların Çalıştırılma Sırası
1. CanMatch
2. CanActivate - CanActivateChild
3. Resolve
4. CanDeactivate