# DEFERRABLE VIEW - @defer
Selector üzerinden birbiri içinde kullanılan componentlerde lazy loading uygulanmasını sağlar.

```typescript
@Component({
  selector: 'app-child',
  standalone: true,
  imports: [CommonModule, ExampleDirective, ExamplePipe],
  template: `Child Component`
})
export class ChildComponent { 
    
}
```

```typescript
@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, ChildComponent],
  template: `
  Home Component <br>
  <app-child></app-child>`
})
export class HomeComponent { 

}
```

```typescript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [HomeComponent],
  template: `
  <app-home></app-home>`
})
export class AppComponent { 

}
```

Verilen kodda Child Component selector üzerinden Home Componentte, Home Component de aynı şekilde selector üzerinden App Componentte kullanılmaktadır.

Bu durumda uygulama ayağa kaldırılıp tarayıcıda açıldığında Home Component içinde Child Component ile onun içerdiği directive ve pipe'lar da yüklenecektir.

Bu tarz selector ile componentlerin kullanıldığı durumlarda `lazy loading` yapmak istenebilir. Bunun için `@defer` dekoratörü ile aşağıdaki şekilde kullanılarak parantez içinde verilen koşula göre lazy loading ile yüklenmesi sağlanabilir,

```typescript
@defer(when state){
    <app-home></app-home>
}
```
Bu yapı ile selector ile referans edilmiş component'in lazy loading ile yüklenmesi sağlanabilir. **Bu componentin kullandığı tüm alt componentler, direktifler ve pipe'lar da lazy loading'e tabi tutulur.**

```typescript
@defer(when state){
    <app-home></app-home>
}
@placeholder {
    ...
}
@loading {
    Loading...
}
@error {
    Error!
}
```

* `@placeholder`: @defer içindeki yükleme koşulu sağlanana kadar farklı bir görüntü isteniyorsa kullanılır.

* `@loading`: Yükleme koşulu karşılandığında component yüklenene kadar kullanılır.

* `@error`: Component yüklenirken bir hatayla karşılaşılırsa kullanılır.

@defer içeriği lazy loading ile; @placeholder, @loading ve @error ise eagerly loading ile yüklenir.

## Titreşim Etkisine Karşı after & minimum
### after
```typescript
@defer(when state){
    <app-home></app-home>
}
@placeholder {
    ...
}
@loading(after 150ms;) {
    Loading...
}
@error {
    Error!
}
```
@defer içerğinin yüklenmesi after ile belirtilen süreden daha kısa sürerse, @loading içeriği görüntülenmeyecektir.

### minimum
```typescript
@defer(when state){
    <app-home></app-home>
}
@placeholder {
    ...
}
@loading(minimum 150ms;) {
    Loading...
}
@error {
    Error!
}
```
@defer içerğinin yüklenmesi minimum ile belirtilen süreden daha hızlıysa, @loading içeriği bu süre zarfında görüntülenecektir.

after ve minimum beraber de kullanılabilir.

```typescript
@loading(after 150ms; minimum 1500ms;) {
    Loading...
}
```

## @defer Koşulları
* `Koşulsuz - on idle`: Hiçbir koşulun olmadığı durumdur. 2 şekilde ifade edilebilir, 
```typescript
@defer{
    <app-home></app-home>
}
```
ya da
```typescript
@defer(on idle){
    <app-home></app-home>
}
```
<hr>

* `when`: Basit bir koşula bağlar.
```typescript
@defer(when state){
    <app-home></app-home>
}
```
when kullanımı, Angulardaki *ngIf direktifine benzetilebilir. İkisi arasındaki fark, *ngIf koşulu true olduğunda template gösterilir ve false olduğunda template kaldırılır. When kullanımında ise koşul bir kere sağlandığında içerik yüklenir, koşul sonradan false olsa bile içerik kaldırılmaz.

<hr>

* `on immediate`: Bloğun yüklenmesi hemen tetiklenir.
```typescript
@defer(on immediate){
    <app-home></app-home>
}
```
<hr>

* `on timer`: Bloğun belirli bir süre sonra yüklenmesini sağlar.
```typescript
@defer(on timer(10s)){
    <app-home></app-home>
}
```

<hr>

* `on hover`: Referance variable ile belirtilen html nesnesinin üzerine gelindiğinde içeriğin yüklenmesini sağlar.
```typescript
<button #btn>Hover to Load</button>
@defer(on hover(btn)){
    <app-home></app-home>
}
```
<hr>

* `on interaction`: Referance variable ile belirtilen html nesnesinin tıklandığında içeriğin yüklenmesini sağlar.
```typescript
<button #btn>Click to Load</button>
@defer(on interaction(btn)){
    <app-home></app-home>
}
```

## Prefetching
Bir component'in yükleme maliyetini önceden karşılayıp, sonrasında lazy loading ile yüklenmesini sağlar.

```typescript
<button #btn>Click to Load</button>
@defer(on interaction(btn); prefetch on idle){
    <app-home></app-home>
}
```
