# MODULES

## Javascript Module ve Angular Module Farkı

Javascript Module, Javascript dilinin bir parçasıdır. Dosya bazındadır. Yani her dosya özünde bir modüldür.

Angular Module, Angular Frameworkünün bir parçasıdır. @NgModule dekoratörü ile işaretlenmiş TypeScript sınıflarıdır. Yani Javascript Module aksine dosya bazlı değil, sınıf bazlıdır.

NgModule Dekoratörünün Genel Yapısı,

```typescript
@NgModule({
  declarations: [
    ...
  ],
  imports: [
    ...
  ],
  providers: [
    ...
  ],

  bootstrap: [
    ...
  ]
})
```

### Declarations
Modul içerisinde tanımlanacak olan component, directive ve pipe'ların bildirildiği kısımdır.

* Bir component, directive ya da pipe birden fazla modülde declare edilemez.

### Imports
Başka bir modülün içe aktarıldığı kısımdır. Başka bir modülün içe aktarılmasıyla o modülde tanımlanmış component, sevice ve directiveler kullanılabilir.

### Providers
Modul düzeyinde `dependency injection` ile kullanılacak servisler bildirilir.

### Exports
Bir modülün component, directive ve pipe'larını dışa aktardığı kısımdır. Böylece bu modül başka bir modül tarafından import edildiğinde, export ettiği yapılar diğer modül tarafından kullanılabilir olur.

### Bootstrap
Uygulamanın başlangıç componentinin belirtildiği kısımdır.

* Eğer modül, uygulamanın ana modülüyse bootstrap tanımlanması zorunludur.

### CLI ile Module Oluşturma
`ng g m moduleName` komutu ile modül oluşturulabilir.

## Lazy Loading
Angular'da uygulamanın performansını güçlendirmek ve başlatma süresini iyileştirmek için modüllerde `Lazy Loading` kullanılır.

İhtiyaç duyulan component veya özelliklerin, yalnızca gerektiği zaman yüklenmesini sağlayan bir tekniktir.

Customers ile alt routeları ve Products ile alt routelarının olduğu bir senaryoda lazy loading uygulayarak, alt componentlerinin yüklenmesinin sadece ana route'a erişim sağlandığında gerçekleşmesini sağlayabiliriz.

```typescript
//customers.module.ts dosyası
@NgModule({
  declarations: [
    AddCustomerComponent,
    ListCustomerComponent,
    DetailCustomerComponent
  ],
  imports: [
    CommonModule,
    RouterModule.forChild([
      { path: "customers/add", component: AddCustomerComponent},
      { path: "customers/:id", component: DetailCustomerComponent},
      { path: "customers", component: ListCustomerComponent}
    ])
  ],
})
```

```typescript
//products.module.ts dosyası
@NgModule({
  declarations: [
    AddProductComponent,
    ListProductComponent,
    DetailProductComponent
  ],
  imports: [
    CommonModule,
    RouterModule.forChild([
      { path: "products/add", component: AddProductComponent},
      { path: "products/:id", component: DetailProductComponent},
      { path: "products", component: ListProductComponent}
    ])
  ],
})
```

```typescript
//app-routing.module.ts dosyası
const routes: Routes = [
  { path: "", component: HomeComponent },
  { path: "customers", loadChildren: () => import("../app/components/customers/customers.module").then(m => m.CustomersModule) },
  { path: "products", loadChildren: () => import("../app/components/products/products.module").then(m => m.ProductsModule) },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})

export class AppRoutingModule { }
```

### Lazy Loading Faydaları
Uygulamadaki tüm modül ve componentleri başta yüklemek yerine ihtiyaç duyulduğu zaman yüklenmelerini sağlayarak ilk yüklemeyi hızlandırır.

Sadece gerekli kaynakların yüklenmesi, client tarafında daha düşük bellek tüketimi ve daha hızlı yanıt süresi sağlar.

## Preloading Strategy
Lazy Loading yapılacak modüllerin, talep edilsin ya da edilmesin, arka planda yavaşça önyükleme yapılmasıdır. Büyük hacimli modüller içeren, lazy loading kullanılan projelerde kullanılır.

Preloading stratejisini kullanabilmek için RouterModule'de aşağıdaki konfigürasyonun yapılması yeterlidir,

```typescript
@NgModule({
  imports: [RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })],
  exports: [RouterModule]
})
```