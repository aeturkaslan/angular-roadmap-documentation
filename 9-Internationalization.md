# INTERNATIONALIZATION
Internationalization (i18n), bir projeyi farklı dillere çevirmek için kullanılır.

## 1- Localize Pakedinin Yüklenmesi
Angular'ın localize pakedi yüklenir,
```
ng add @angular/localize
```

## 2- angular.json Dosyasında Manuel Eklemeler
angular.json içerisine manuel olarak bir ekleme yapılması gerekiyor, 

```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "angular-internationalization": {
      "i18n": { // #1 - i18n property
        "sourceLocale": "en-US",
        "locales": {
          "tr-TR": "src/locale/messages.tr.xlf"
        }
      },
      "projectType": "application",
      "schematics": {},
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "localize": ["tr-TR"], // #2
            "outputPath": "dist/angular-internationalization",
            "index": "src/index.html",
            "browser": "src/main.ts",
            ...
          },
        }
      }
    }
  }
}

```

## 3- Çevirilecek Kısımların i18n ile İşaretlenmesi
Çeviri yapılacak html elementlerine, `i18n` attribute'u eklenir.

```html
<div class="container">
  <div class="card" style="width: 18rem;">
    <img alt="confirmation image" i18n-alt src="./assets/1.jpg" class="card-img-top">
    <div class="card-body">
      <h5 i18n class="card-title">Your Order Is Confirmed</h5>
      <p i18n class="card-text">Date: {{today | date}} , Price: {{529 | currency}}</p>
      <a i18n href="#" class="btn btn-primary">Confirm</a>
    </div>
  </div>
</div>
```

## 4- Çeviri Dosyasının Oluşturulması
i18n ile işaretlenmiş html elementleri, `ng extract-i18n` komutu ile elde edilir.

```
ng extract-i18n --output-path src/locale
```
Bu komut, i18n ile işaretlenmiş kısımlardan oluşan bir kaynak dil dosyası oluşturulmasını sağlar.

Oluşturulan dosya kopyalanıp yeniden adlandırılarak bir çeviri dosyası oluşturulur.

```
copy src/locale/messages.xlf src/locale/messages.tr.xlf
```

## 5- Çevirinin Yapılması
```html
<trans-unit id="introductionHeader" datatype="html">
    <source>Hello i18n!</source>
    <note priority="1" from="description">An introduction header for this sample</note>
    <note priority="1" from="meaning">User welcome</note>
</trans-unit>
```
`<trans-unit>` ile işaretli kısım, çevirilecek kısmı ve yapılacak bu çeviri için düşülmüş notları içerir.

`<source></source>` elementi bir alt satıra kopyalanıp `<target></target>` olarak yeniden adlandırılır. Metin ise hedef dile çevrilir.

```html
<trans-unit id="introductionHeader" datatype="html">
    <source>Hello i18n!</source>
    <target>Merhaba i18n!</target>
    <note priority="1" from="description">An introduction header for this sample</note>
    <note priority="1" from="meaning">User welcome</note>
</trans-unit>
```
* Daha karışık çevirilerde; i18n ile beraber kullanılan description ve meaningler, çeviride daha doğru kelime seçimleri yapılmasında önemlidir.
