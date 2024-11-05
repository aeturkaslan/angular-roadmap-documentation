# HttpClient
Angular bir frontend mimarisidir ve verisel işlemler için genellikle API'lar kullanılır. Bunun için API'lara HTTP isteklerinde bulunulması gerekir. Bunun için bir Angular kütüphanesi olan `HttpClient` kütüphanesi kullanılabilir.

HttpClient kütüphanesi API'lara GET, POST, PUT, DELETE istekleri atmamızı ve bu isteklerin sonuçlarını almamızı sağlayan hazır bir kütüphanedir.

Kullanılabilmesi için `HttpClientModule` 'ün uygulamanın ana modülüne ya da standalone ise main.ts dosyasında `bootstrapApplication` fonksiyonu üzerinden eklenmesi gerekir.

```typescript
//main.ts dosyası
...
bootstrapApplication(AppComponent, {
    providers: [
        importProvidersFrom(HttpClientModule)
    ]
})
```

```typescript
@Component({
    selector: 'app-root',
    template: ``,
    standalone: true
})

export class AppComponent {
    constructor(private httpClient: HttpClient) {
        httpClient.get("https://jsonplaceholder.typicode.com/posts")
        .subscribe({
            next: datas => console.log(datas),
            error: error => console.log(error)
        })
    }
}
```

## HttpParams Fonksiyonları
İstek gönderilecek URL'ye parametre eklememizi sağlar.

### Set Fonksiyonu

```typescript
getPosts() {
    const params = new HttpParams()
        .set("id", 1)
        .set("sort", "asc");

        this.httpClient.get("https://jsonplaceholder.typicode.com/posts", {params})
        .subscribe({
            next: datas => console.log(datas),
            error: error => console.log(error)
        })
}
```

### Append Fonksiyonu
URL'e birden fazla aynı isimde parametre eklenmesini sağlar.

```typescript
let params = new HttpParams()
    .set("id", 1)
    .append("id", 2)
    .set("sort", "asc");
```

### Has, Get ve GetAll Fonksiyonları
* Has: Belirtilen parametre karşılığında değer olup olmadığını söyler.
* Get: Verilen key'e karşılık ilk parametre değerini getirir.
* GetAll: Verilen key'e karşılık tüm parametre değerlerini getirir.

### Keys ve Delete Fonksiyonları
* Keys: Tüm parametre keylerini getirir.
* Delete: Verilen key ve value'ya karşılık parametreyi siler.

### fromString
URL parametrelerini string olarak iletmenin pratik yoludur.

```typescript
let params = new HttpParams({fromString: `id=1&sort=asc`});
```

### fromObject
URL parametrelerini object olarak iletmenin pratik yoludur.

```typescript
let params = new HttpParams({fromObject: {id: 1, sort: "asc" } });
```

* HttpParams kullanmadan da URL parametreleri kullanılabilir.

```typescript
this.httpClient.get("https://jsonplaceholder.typicode.com/posts?id=1&sort=asc")
```

## Http Interceptor
Uygulama bazlı yapılan HTTP requestlerin ve responseların yakalanarak müdahalede bulunulabilmesini sağlayan yapıdır.

Genellikle yetkilendirme gerektiren API'lara yapılan istek süreçlerinde kullanılacak `Access Token` değerinin ilgili requestin header'ına eklenmesinde kullanılır.

`ng g interceptor interceptorName` komutuyla interceptor oluşturulabilir.

```typescript
//main.ts dosyası
...
bootstrapApplication(AppComponent, {
    providers: [
        importProvidersFrom(HttpClientModule)
        { provide HTTP_INTERCEPTORS, useClass: MyInterceptor, multi: true}
    ]
})
```

```typescript
@Component({
    selector: 'app-root',
    template: `<button (click)="click()">Tıklayın</button>`,
    standalone: true
})

export class AppComponent {
    constructor(private httpClient: HttpClient) { }

    click() {
        this.httpClient.get("https://jsonplaceholder.typicode.com/posts")
        .subscribe({
            next: datas => console.log(datas),
        })
    }
}
```

```typescript
//my.interceptor.ts dosyası
...
@Injectable()
export class MyInterceptor implements HttpInterceptor {
    constructor () {}

    intercept(request: HttpRequest<unknown>, next: HttpHandler):
    Observable<HttpEvent<unknown>> {
        console.log('HTTP Interceptor tetiklendi...');

        const newRequest = request.clone({
            setHeaders: {
                "Authorization": "Bearer nfJfaghFLFhsfha"
            },
            body: {
                a: 'b'
            }
        })

        return next.handle(request);
    }
}
```

# SSR (Server-Side Rendering)
Tarayıcı tarafında çalışan ve Javascript tarafından oluşturulan sayfa içerikleri, sayfanın yüklenme süresini uzatır ve arama motorları tarafından erişilebilir değillerdir.

Server-Side Rendering, bu süreci hızlandırır ve içeriği arama motorları açısından erişilebilir kılmak için kullanılır.

Temel olarak bu, HTML sayfasının tarayıcıda değil, sunucuya gönderilip orada oluşturulması ile gerçekleştirilir.

## Angular Projesinde SSR kullanma
Angular 17 ile beraber yeni bir Angular projesi oluşturulurken, SSR eklemek istenip istenilmediği sorulur.

Bunun dışında projeye sonradan Angular Universal eklenerek SSR kullanılabilir. `ng add @angular/ssr` komutu ile bu işlem gerçekleştirilebilir.