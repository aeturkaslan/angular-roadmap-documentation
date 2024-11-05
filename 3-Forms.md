# FORMS
Formlar, kullanıcı ile veri olarak etkileşime girmenin bir yoludur. Angular Forms yapısını oluşturmanın 2 yolu vardır:
* Template-Driven Forms
* Model-Driven (Reactive) Forms

## Template-Driven Forms
Form elemanlarının her biri 'ngModel' direktifi ile işaretlenerek çalışma sergilenir.

Formun tasarım ve yapılandırılması tamamen template üzerinde gerçekleşir.


## Model-Driven (Reactive) Forms
Model-driven form'da form yapısı Component'te nesne olarak tanımlanır ve form'a binding işlemi ile bağlanır.

Reactive olarak da adlandırılmasının sebebi, form elemanları ve form elemanlarındaki verilerdeki değişikliklerin modelde dinamik olarak takip edilmesidir.

## Angular Form Konseptleri

### FormGroup
Formun kendisini temsil eder.

### FormArray
Form içerisinde dinamik olarak oluşturulan kontolleri temsil eden nesnedir.

### FormControl
Form içinde bulunan, kullanıcıdan veri alınmasını sağlayan tek bir kontrolü temsil eder.

### FormBuilder
Önceki 3 nesneyi oluşturmayı kolaylaştıran bir servistir. İçinde hazır fonksiyonlar bulundurur.

## Template-Driven Yaklaşımıyla Form Oluşturma
1. FormsModule import edilmelidir.
2. Template-driven form olarak kullanılacak form, `ngForm` directive'i ile işaretlenmelidir. Bu directive sayesinde form elemanı otomatik olarak algılanıp bind edilir.
3. Form içerisindeki kullanılacak kontroller `ngModel` directive'i ile işaretlenir.
4. Doldurulan form verilerinin component'a gönderilebilmesi için `ngSubmit` event'i kullanılır.

```html
  <form #frm="ngForm" (ngSubmit)="onSubmit(frm.value)">
    <input type="text" name="name" placeholder="Name" ngModel> <br>
    <input type="text" name="surname" placeholder="Surname" ngModel> <br>
    <input type="email" name="email" placeholder="Email" ngModel> <br>
    <input type="tel" name="tel" placeholder="Tel"> <br> <!--tel'i ngModel ile bağlamadık-->
    <div ngModelGroup="address">
      <input type="text" name="country" placeholder="Country" ngModel> <br>
      <input type="text" name="city" placeholder="City" ngModel> <br>
      <input type="text" name="address" placeholder="Address" ngModel> <br>
    </div>

    <button>Send</button>
  </form>
```

```typescript
import { Component, ViewChild } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({...})
export class TemplatedrivenComponent {
  @ViewChild("frm", { static: true }) frm: NgForm;  //Form'u Component class'ında referans etmek için

  onSubmit(data) {
    console.log(`Value: ${this.frm.value}`);
    console.log(`Valid: ${this.frm.valid}`);
    console.log(`Touched: ${this.frm.touched}`);
    console.log(`Submitted: ${this.frm.submitted}`);

    console.log(data);

    console.log(this.frm);
    console.log(this.frm.form);
    console.log(this.frm.controls);
  }
}

```

## Model-Driven Yaklaşımıyla Form Oluşturma
1. ReactiveFormsModule import edilmelidir.
2. Oluşturulacak form'un modeli oluşturulur ve gerekli FormControl'ler tanımlanır. Bunun için `FormBuilder` nesnesi kullanılabilir.
3. HTML kısmında form bütünsel olarak tasarlanır. Form etiketi `formGroup` directive'i ile oluşturulan modele, form kontrolleri ise modeldeki ilgili FormControl'lerine `formControlName` directive'i ile bağlanır.
4. Kullanıcı tarafından doldurulan bu modelin gönderilmesi için `ngSubmit` event'ine bir fonksiyon tanımlanır.

```html
<form [formGroup]="frm" (ngSubmit)="onSubmit()">
    <input type="text" placeholder="Name" formControlName="name"> <br>
    <input type="text" placeholder="Surname" formControlName="surname"> <br>
    <input type="text" placeholder="Email" formControlName="email"> <br>
    <input type="text" placeholder="Tel" formControlName="tel"> <br>
    <div formGroupName="address">
      <input type="text" placeholder="Country" formControlName="country"> <br>
      <input type="text" placeholder="City" formControlName="city"> <br>
      <input type="text" placeholder="Address" formControlName="address"> <br>
    </div>

    <button>Send</button>
  </form>
```


```typescript
import { Component } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';

@Component({...})
export class ModeldrivenComponent {
  frm: FormGroup;
  constructor(private formBuilder: FormBuilder) {
    this.frm = formBuilder.group({
      name: [""],
      surname: [""],
      email: [""],
      tel: [""],
      address: formBuilder.group({
        country: [""],
        city: [""],
        address: [""]
      })
    })
  }

  onSubmit() {
    console.log(this.frm.value);
  }
}
```

