# Kode Program

## Modul Utama

### app-routing.module.ts

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { ForgotComponent } from './auth/forgot/forgot.component';
import { LoginComponent } from './auth/login/login.component';
import { RegisterComponent } from './auth/register/register.component';
import { ResetPasswordComponent } from './auth/reset-password/reset-password.component';

const routes: Routes = [
  {
    path:'login',
    component: LoginComponent
  },
  {
    path: 'register',
    component: RegisterComponent
  },
  {
    path: 'forgot',
    component: ForgotComponent
  },
  {
    path:'reset/:token',
    component:ResetPasswordComponent
  },
  {
    path:'public',
    loadChildren: () => import('./public/public.module').then(mod=>mod.SchoolModule)
  },
  {
    path:'',
    pathMatch:'full',
    redirectTo:'login'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

### app.component.html 

```html
<router-outlet></router-outlet>
```

###  app.component.spec.ts

```typescript
import { TestBed } from '@angular/core/testing';
import { RouterTestingModule } from '@angular/router/testing';
import { AppComponent } from './app.component';

describe('AppComponent', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [
        RouterTestingModule
      ],
      declarations: [
        AppComponent
      ],
    }).compileComponents();
  });

  it('should create the app', () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });

  it(`should have as title 'goopi'`, () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app.title).toEqual('goopi');
  });

  it('should render title', () => {
    const fixture = TestBed.createComponent(AppComponent);
    fixture.detectChanges();
    const compiled = fixture.nativeElement;
    expect(compiled.querySelector('.content span').textContent).toContain('goopi app is running!');
  });
});

```

### app.component.ts 

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'goopi';
}

```

### app.module.ts

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './auth/login/login.component';
import { RegisterComponent } from './auth/register/register.component';
import { ForgotComponent } from './auth/forgot/forgot.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

//Angular Module
import {MatFormFieldModule} from '@angular/material/form-field';
import {MatCardModule} from '@angular/material/card';
import {MatButtonModule} from '@angular/material/button';
import {MatInputModule} from '@angular/material/input';
import {MatSelectModule} from '@angular/material/select';
import {MatChipsModule} from '@angular/material/chips';
import {MatDialogModule} from '@angular/material/dialog';
import {MatAutocompleteModule} from '@angular/material/autocomplete';
import { ImageUploaderComponent } from './media/image-uploader/image-uploader.component';
import { HttpClientModule } from '@angular/common/http';
import { ResetPasswordComponent } from './auth/reset-password/reset-password.component';
import { ImageCropperModule } from 'ngx-image-cropper';

// Import ngx-barcode module
import { NgxBarcodeModule } from 'ngx-barcode';

@NgModule({
  declarations: [
    AppComponent,
    LoginComponent,
    RegisterComponent,
    ForgotComponent,   
    ResetPasswordComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    MatFormFieldModule,
    MatCardModule,
    MatButtonModule,
    FormsModule, 
    ReactiveFormsModule,
    MatInputModule,
    MatSelectModule,
    MatChipsModule,
    MatDialogModule,
    MatAutocompleteModule,
    HttpClientModule,
    NgxBarcodeModule,
    ImageCropperModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Authentication

### Forgot

#### forgot.component.html

```html
<div class="uk-section uk-section-muted uk-flex uk-flex-middle uk-animation-fade" uk-height-viewport>
	<div class="uk-width-1-1">
		<div class="uk-container">
			<div class="uk-grid-margin uk-grid uk-grid-stack" uk-grid>
				<div class="uk-width-1-1@m">
					<div class="uk-margin uk-width-large uk-margin-auto uk-card uk-card-default uk-card-body uk-box-shadow-large">
						<div class="uk-margin-bottom">
							<div><img class="uk-align-center" src="assets/imgs/logo.png" style="width: 200px;"></div>
						</div>

            <h1 style="text-align: center;">Lupa Sandi</h1>
            <p>Masukkan email terdaftar untuk menerima link pembaruan kata sandi</p>

						<mat-form-field class="uk-width-1-1" appearance="outline">
							<mat-label>Email</mat-label>
							<input matInput [(ngModel)]="email" type="text" >
						</mat-form-field>

            <div class="uk-margin">
              <button mat-flat-button color="primary" class="uk-width-1-1" style="height: 50px;" (click)="send(email)" [disabled]="loading">{{loading ? 'Mengirim..':'Kirimkan link pembaruan'}}</button>
            </div>

            <div class="uk-margin">
              <div>Sudah punya Akun ? <a mat-flat-button routerLink="/login">Masuk</a></div>
            </div>
							<div style="padding-bottom: 20px;"></div>


					</div>
				</div>
			</div>
		</div>
	</div>
</div>
```

#### forgot.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'app-forgot',
  templateUrl: './forgot.component.html',
  styleUrls: ['./forgot.component.scss']
})
export class ForgotComponent implements OnInit {

  constructor(
    public api:ApiService
  ) { }

  ngOnInit(): void {
  }

  email:any;
  loading:boolean;
  send(email)
  {
    email = email.toLowerCase();
    this.loading=true;
    this.api.post('auth/reset',{email:email}).subscribe(res=>{
      this.loading=false;
      alert('Pembaruan password terkirim melalui email.');
    },err=>{
      this.loading=false;
      alert('Tidak terkirim. Pastikan email Anda terdaftar pada sistem.');
    });
  }

}

```

#### forgot.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { ForgotComponent } from './forgot.component';

describe('ForgotComponent', () => {
  let component: ForgotComponent;
  let fixture: ComponentFixture<ForgotComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ForgotComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(ForgotComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

```

#### forgot.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'app-forgot',
  templateUrl: './forgot.component.html',
  styleUrls: ['./forgot.component.scss']
})
export class ForgotComponent implements OnInit {

  constructor(
    public api:ApiService
  ) { }

  ngOnInit(): void {
  }

  email:any;
  loading:boolean;
  send(email)
  {
    email = email.toLowerCase();
    this.loading=true;
    this.api.post('auth/reset',{email:email}).subscribe(res=>{
      this.loading=false;
      alert('Pembaruan password terkirim melalui email.');
    },err=>{
      this.loading=false;
      alert('Tidak terkirim. Pastikan email Anda terdaftar pada sistem.');
    });
  }

}

```


### Login

#### login.component.html

```html
<div class="uk-section uk-section-muted uk-flex uk-flex-middle uk-animation-fade" uk-height-viewport>
  <div class="uk-container">
    <div class="uk-card uk-card-default uk-border-rounded">
      <div class="uk-grid">
        <div class="uk-width-1-2@m">
          <div class="bg-img">
            <div class="uk-margin uk-padding top">
							<div><img class="uk-align-center" src="assets/imgs/image.png" style="width: 100px;"></div>
              <div style="text-align: center; color: white;">
                <h1 style="color: white;">Aisyiyah</h1>
                <p>Gerakan Perempuan Muhammadiyah dengan Misi Dakwah Amar Ma'ruf Nahi Mungkar dan Tajdid </p>
              </div>


						</div>
          </div>
        </div>
        <div class="uk-width-1-2@m">
          <div class="uk-padding">
            <h1>Login</h1>
            <mat-form-field class="uk-width-1-1" appearance="outline">
              <mat-label>Email</mat-label>
              <input matInput [(ngModel)]="user.email" (keydown.enter)="enterLogin($event)" type="text" >
            </mat-form-field>

            <mat-form-field class="uk-width-1-1" appearance="outline">
              <mat-label>Password</mat-label>
              <input matInput [(ngModel)]="user.password" (keydown.enter)="enterLogin($event)" type="password" >
            </mat-form-field>

            <div *ngIf="success == true" class="uk-alert alert-info uk-alert-success">
              Login sukses. Tunggu sampai diarahkan ke halaman Dashboard.
            </div>

            <div *ngIf="success == false" class="uk-alert alert-info uk-alert-danger">
              Tidak dapat login. Pastikan email dan password benar.
            </div>

            <div *ngIf="nonaktif == true" class="uk-alert alert-info uk-alert-danger">
              Status pengguna nonaktif. Silahkan hubungi admin PP Aisyiyah.
            </div>

            <div class="uk-margin">
              <button mat-flat-button color="primary" class="uk-width-1-1" style="height: 50px;" (click)="login()">{{loading ? 'Mohon Tunggu..':'Masuk'}}</button>
            </div>

            <button routerLink="/forgot" mat-button>Lupa Password</button>
            <button routerLink="/register" mat-button hidden >Register</button>
          </div>

        </div>
      </div>
    </div>
  </div>
</div>
```

#### login.component.spec.ts 

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { LoginComponent } from './login.component';

describe('LoginComponent', () => {
  let component: LoginComponent;
  let fixture: ComponentFixture<LoginComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ LoginComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(LoginComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

#### login.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ApiService } from 'src/app/services/api.service';
import { Router } from '@angular/router';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  constructor(
    public api: ApiService,
    public router:Router
  ) { }

  ngOnInit(): void {
    this.cekLogin();
  }
  user:any={
    email:'',
    password:''
  };
  loading:boolean;
  login()
  {
    this.user.email = this.user.email.toLowerCase();
    this.loading=true;
    this.api.post('auth/login',this.user).subscribe(res=>{      
      this.loading=false;
      localStorage.setItem('ppaToken',JSON.stringify(res));
      this.redirect(res);
    },err=>{
      console.log(err)
      this.loading=false;
      this.success = false;
      // setTimeout(function () {
      //   location.reload();
      // }, 1000);
    })
  }

  enterLogin(event) {
    if (event.keyCode === 13) {
      this.login();
    }
  }
  
  cekLogin()
  {    
    this.api.me().subscribe(res=>{
      this.redirect(res);
    })
  }

  nonaktif:boolean = false;
  success:boolean;
  redirect(user)
  {
    if(user.is_active == 1) {
      this.success = true;
      var that = this;
      setTimeout(function () {
        that.router.navigate(['/public/dashboard']);
      }, 1000);
    } else {
      this.nonaktif = true;
      setTimeout(function () {
        location.reload();
      }, 1000);
    }
  }

}

```

### Register

#### register.component.html 

```html
<div class="uk-section uk-section-muted uk-flex uk-flex-middle uk-animation-fade" uk-height-viewport>
	<div class="uk-width-1-1">
		<div class="uk-container">
			<div class="uk-grid-margin uk-grid uk-grid-stack" uk-grid>
				<div class="uk-width-1-1@m">
					<div class="uk-margin uk-width-large uk-margin-auto uk-card uk-card-default uk-card-body uk-box-shadow-large">
						<div class="uk-margin-bottom">
							<div><img class="uk-align-center" src="assets/imgs/logo.png" style="width: 200px;"></div>
            </div>

            <h1 style="text-align: center;">Registrasi</h1>
            <div class="uk-grid-small uk-form-stacked" uk-grid>
              <div class="uk-width-expand@m">
                <mat-form-field class="uk-width-1-1" appearance="outline">
                  <mat-label>Nama</mat-label>
                  <input matInput [(ngModel)]="user.name" type="text" required #name="ngModel">
                </mat-form-field>

                <mat-form-field class="uk-width-1-1" appearance="outline">
                  <mat-label>Email</mat-label>
                  <input matInput [(ngModel)]="user.email" type="text" required #email="ngModel" placeholder="email@domain.com">
                </mat-form-field>

                <mat-form-field class="uk-width-1-1" appearance="outline">
                  <mat-label>Kata Sandi</mat-label>
                  <input matInput type="password" [(ngModel)]="user.password" required minlength="6" #password="ngModel">
                </mat-form-field>

                <mat-form-field class="uk-width-1-1" appearance="outline">
                  <mat-label>Tulis Ulang Kata Sandi</mat-label>
                  <input matInput type="password" [(ngModel)]="passConfirm" required #passConf="ngModel" (ngModelChange)="cekPw()">
                </mat-form-field>


                <div *ngIf="msg != null" style="padding: 30px 20px 30px 20px">
                  <p>
                    {{msg}}
                  </p>
                </div>
              </div>
            </div>

							<div class="uk-margin">
								<button mat-flat-button color="primary" class="uk-width-1-1" style="height: 50px;" (click)="cekUser()" [disabled]="pwNotMatch == true || name.invalid || email.invalid || password.invalid || passConf.invalid || password.value != passConf.value || loading">Daftar</button>
							</div>

              <div class="uk-margin">
                <div>Sudah punya Akun ? <a mat-flat-button routerLink="/login">Masuk</a></div>
							</div>

					</div>
				</div>
			</div>
		</div>
	</div>
</div>
```

#### register.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { RegisterComponent } from './register.component';

describe('RegisterComponent', () => {
  let component: RegisterComponent;
  let fixture: ComponentFixture<RegisterComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ RegisterComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(RegisterComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

#### register.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ApiService } from 'src/app/services/api.service';
import { Router } from '@angular/router';

@Component({
  selector: 'app-register',
  templateUrl: './register.component.html',
  styleUrls: ['./register.component.scss']
})
export class RegisterComponent implements OnInit {

  constructor(
    public api: ApiService,
    public router: Router
  ) { }

  ngOnInit(): void {
  }

  user: any = {};
  passConfirm: any;
  loading: boolean;

  cekUser()
  {    
    this.user.email = this.user.email.toLowerCase();
    this.loading = true;
    this.api.post('auth/register', this.user).subscribe(res=>{
      this.loading = false;
      this.redirect(res);
    },err=>{
      this.loading = false;
    })
  }

  msg: any;
  redirect(res)
  {
    if(res == true ){
      this.msg = 'Registrasi berhasil. Sekarang Anda dapat melakukan login.';
    } else if(res == 'exist') {
      this.msg = 'Email sudah digunakan pengguna lain.';
    } else{
      this.msg = 'Registrasi gagal. Pastikan email yang digunakan sudah benar';
    }
    setTimeout(()=>{
      this.msg = null;
      this.router.navigate(['/login']);
    }, 3000);
  }

  pwNotMatch:boolean = false;
  cekPw() {
    if(this.passConfirm != this.user.password) {
      this.msg = 'Password Tidak Sama!';
      this.pwNotMatch = true;
    } else {
      this.msg = null;
      this.pwNotMatch = false;
    }
  }

}

```

### Reset Password

#### reset-password.component.html

```html
<div class="uk-flex uk-flex-middle uk-flex-center uk-height-viewport">  
    <mat-card class="uk-width-1-3@m">
        <div class="uk-flex uk-flex-center uk-margin-medium-bottom">
            <h3>Pembaruan Password</h3>
        </div>
      <mat-card-content>
        <mat-form-field class="uk-width-1-1">
        <mat-label>Password Baru</mat-label>
        <input matInput type="password" placeholder="Your password" [(ngModel)]="user.p1" required minlength="6" #password="ngModel">
        </mat-form-field>

        <mat-form-field class="uk-width-1-1">
        <mat-label>Re-type Password</mat-label>
        <input matInput type="password" placeholder="Your password" [(ngModel)]="user.p2" required #passConf="ngModel">
        </mat-form-field>

        <div class="uk-flex uk-flex-right">
            <div class="uk-width-expand">
            <button mat-button routerLink="/login">Login</button>
            </div>
            <button mat-raised-button color="primary" [disabled]="password.invalid || passConf.invalid || password.value != passConf.value || loading" (click)="reset()">{{loading ? 'TUNGGU..':'RESET'}}</button>
        </div>
      </mat-card-content>
    </mat-card>
</div>
```

#### reset-password.component.spec.ts 

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { ResetPasswordComponent } from './reset-password.component';

describe('ResetPasswordComponent', () => {
  let component: ResetPasswordComponent;
  let fixture: ComponentFixture<ResetPasswordComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ResetPasswordComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(ResetPasswordComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

#### reset-password.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'app-reset-password',
  templateUrl: './reset-password.component.html',
  styleUrls: ['./reset-password.component.scss']
})
export class ResetPasswordComponent implements OnInit {

  constructor(
    public routes: ActivatedRoute,
    public api: ApiService,
    public router:Router
  ) { }

  ngOnInit(): void {
    var token=this.routes.snapshot.paramMap.get('token');
    this.checkToken(token);
  }

  userData:any;
  checkToken(token)
  {    
    this.api.get('auth/reset?token='+token).subscribe(res=>{
      this.userData=res;
    },err=>{
      //this.router.navigate(['auth/login']);
    })
  }

  user:any={p1:'',p2:''};

  loading:boolean;
  reset()
  {
    if(this.user.p1 != '' && this.user.p1 == this.user.p2)
    {
      this.loading=true;
      this.api.put('auth/reset',{password:this.user.p1, email:this.userData.email}).subscribe(res=>{
        alert('Pembaruan password berhasil');
        this.loading=false;
        this.router.navigate(['/login']);
      },err=>{
        alert('Tidak dapat memperbarui password');
        this.loading=false;
      });
    }else{
      alert('Password tidak cocok');
    }
  }

}
```

## Material Design

### material.ts 

```typescript
import {MatCheckboxModule} from '@angular/material/checkbox';
import {MatButtonModule} from '@angular/material/button';
import {MatInputModule} from '@angular/material/input';
import {MatAutocompleteModule} from '@angular/material/autocomplete';
import {MatDatepickerModule} from '@angular/material/datepicker';
import {MatFormFieldModule} from '@angular/material/form-field';
import {MatRadioModule} from '@angular/material/radio';
import {MatSelectModule} from '@angular/material/select';
import {MatSliderModule} from '@angular/material/slider';
import {MatSlideToggleModule} from '@angular/material/slide-toggle';
import {MatMenuModule} from '@angular/material/menu';
import {MatSidenavModule} from '@angular/material/sidenav';
import {MatToolbarModule} from '@angular/material/toolbar';
import {MatListModule} from '@angular/material/list';
import {MatGridListModule} from '@angular/material/grid-list';
import {MatCardModule} from '@angular/material/card';
import {MatStepperModule} from '@angular/material/stepper';
import {MatTabsModule} from '@angular/material/tabs';
import {MatExpansionModule} from '@angular/material/expansion';
import {MatButtonToggleModule} from '@angular/material/button-toggle';
import {MatChipsModule} from '@angular/material/chips';
import {MatIconModule} from '@angular/material/icon';
import {MatProgressSpinnerModule} from '@angular/material/progress-spinner';
import {MatProgressBarModule} from '@angular/material/progress-bar';
import {MatDialogModule} from '@angular/material/dialog';
import {MatTooltipModule} from '@angular/material/tooltip';
import {MatSnackBarModule} from '@angular/material/snack-bar';
import {MatTableModule} from '@angular/material/table';
import {MatSortModule} from '@angular/material/sort';
import {MatPaginatorModule} from '@angular/material/paginator';

export const MaterialDesign=[
    MatCheckboxModule,
    MatCheckboxModule,
    MatButtonModule,
    MatInputModule,
    MatAutocompleteModule,
    MatDatepickerModule,
    MatFormFieldModule,
    MatRadioModule,
    MatSelectModule,
    MatSliderModule,
    MatSlideToggleModule,
    MatMenuModule,
    MatSidenavModule,
    MatToolbarModule,
    MatListModule,
    MatGridListModule,
    MatCardModule,
    MatStepperModule,
    MatTabsModule,
    MatExpansionModule,
    MatButtonToggleModule,
    MatChipsModule,
    MatIconModule,
    MatProgressSpinnerModule,
    MatProgressBarModule,
    MatDialogModule,
    MatTooltipModule,
    MatSnackBarModule,
    MatTableModule,
    MatSortModule,
    MatPaginatorModule
]
```

## Media 

### Image Uploader

#### image-uploader.component.html

```html
<h1 mat-dialog-title>Lampirkan Foto</h1>

<div mat-dialog-content>
  <!-- <img src="{{reviewImg == undefined ? 'assets/imgs/add_image.png':reviewImg}}" alt=""> -->
  
  <!-- <input #imageInput
  type="file"
  accept="image/png"
  (change)="processFile(imageInput)" style="display: none"> -->

  <input #imageInput
  type="file"
  accept="image/png"
  (change)="fileChangeEvent($event)" style="display: none">

  <image-cropper class="container-image"
  [imageChangedEvent]="imageChangedEvent"
  [maintainAspectRatio]="keepRatio"
  [aspectRatio]="ratio"
  format="png"
  [resizeToWidth]="150"
  (imageCropped)="imageCropped($event)"
  (imageLoaded)="imageLoaded()"
  (cropperReady)="cropperReady()"
  (loadImageFailed)="loadImageFailed()"
></image-cropper>

  <!-- <mat-hint><i style="color: red;">*</i> Pastikan format foto/gambar yang dikirim adalah <i>png</i></mat-hint> -->
  <div class="uk-flex uk-margin uk-flex-middle uk-text-center">
    <div class="uk-width-expand" style="margin-right: 10px;">
      <div style="padding: 5px; outline: 2px dashed #ccc; text-align: center;" (click)="imageInput.click()">{{imageChangedEvent ? imageChangedEvent.target.files[0].name : 'Pilih File'}}</div>
    </div>
  </div>


</div>

<div mat-dialog-actions>
  <span class="uk-width-expand"></span>
  <button mat-button (click)="onNoClick()">Batal</button>
  <button mat-raised-button color="primary" [disabled]="formatOk == false" (click)="unggahFoto()">{{loading ? 'Mohon Tunggu...':'Gunakan Foto'}}</button>
</div>
```

#### image-uploader.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { ImageUploaderComponent } from './image-uploader.component';

describe('ImageUploaderComponent', () => {
  let component: ImageUploaderComponent;
  let fixture: ComponentFixture<ImageUploaderComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ImageUploaderComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(ImageUploaderComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

#### image-uploader.component.ts

```typescript
import { Component, Inject, OnInit } from '@angular/core';
import { MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { ImageCroppedEvent } from 'ngx-image-cropper';
import { ApiService } from 'src/app/services/api.service';

class ImageSnippet {
  pending: boolean = false;
  status: string = 'init';

  constructor(public src: string, public file: File) {}
}

@Component({
  selector: 'app-image-uploader',
  templateUrl: './image-uploader.component.html',
  styleUrls: ['./image-uploader.component.scss']
})
export class ImageUploaderComponent implements OnInit {
  ratio:any = 1;
  keepRatio: boolean = true;
  path:any;
  id:any;
  constructor(
    public api:ApiService,
    public dialogRef: MatDialogRef<ImageUploaderComponent>,
    @Inject(MAT_DIALOG_DATA) public sourceData: any
  ) {
    //this.id = sourceData.id;
    this.ratio = sourceData == null ? 1 : sourceData.ratio;
    this.keepRatio =  sourceData == null ? false : sourceData.keepRatio;
  }

  ngOnInit(): void {
  }
  
  imageChangedEvent: any = '';
  croppedImage: any = '';
  
  fileChangeEvent(event: any): void {
      this.imageChangedEvent = event;
  }

  imageCropped(event: ImageCroppedEvent) {
    this.croppedImage = event.base64;
    var lowerCase = this.croppedImage.toLowerCase();
    if (lowerCase.indexOf("png") !== -1) {
      this.formatOk = true;
    } else {
      this.formatOk = false;
    }
  }

  imageLoaded() {
      // show cropper
  }

  cropperReady() {
      // cropper ready
  }

  loadImageFailed() {
      // show message
  }

  formatOk: boolean = false;
  loading:boolean;
  unggahFoto() {
    this.loading = true;
    this.dialogRef.close(this.croppedImage);
  }

  onNoClick(): void {
    this.dialogRef.close();
  }

}

var signatures = {
  iVBORw0KGgo: "image/png"
};

function detectMimeType(b64) {
  for (var s in signatures) {
    if (b64.indexOf(s) === 0) {
      return signatures[s];
    }
  }
}
```

## Admin

### public.component.html 

```html
<ng-container>
  <div class="uk-flex">
    <div style="width: 300px;"  *ngIf="left.opened">
      <mat-toolbar class="border-bottom border-right">
        <button mat-icon-button (click)="left.toggle()">
          <mat-icon>menu</mat-icon>
        </button>
        <span>PP Aisyiyah</span>
      </mat-toolbar>
      <mat-divider></mat-divider>
    </div>
    <div class="spacer">
      <mat-toolbar class="border-bottom">
        <button mat-icon-button (click)="left.toggle()"  *ngIf="!left.opened">
          <mat-icon>menu</mat-icon>
        </button>
        <span>{{pageTitle}}</span>
        <div class="spacer"></div>
        <div style="text-align: right; padding-right: 10px;">
          <small>{{userData == undefined ? 'PP Aisyiyah':userData.name}}</small>
        </div>
        <button mat-icon-button [matMenuTriggerFor]="menu">
          <mat-icon>more_vert</mat-icon>
        </button>
        <mat-menu #menu="matMenu">
          <button (click)="logout()" mat-menu-item> <mat-icon>logout</mat-icon> Keluar</button>
        </mat-menu>

      </mat-toolbar>
    </div>
  </div>
  <mat-sidenav-container class="example-container">

      <mat-sidenav #left mode="side" opened autosize position="start">

        <div style="position: absolute; top:0px;bottom: 60px; width: 100%; overflow: auto;">

        <!--Header-->


        <div>
            <nav class="uk-navbar-container" uk-navbar style="background-color: white;">
              <div class="uk-text-center" style="padding-top:20px; padding-bottom: 20px;">
                <img src="assets/imgs/logo.png" width="60%">
                <h3 matLine>{{userData.role_name}} {{userData.nama_area == undefined ? '':userData.nama_area}}</h3>
              </div>
            </nav>
        </div>


        <!--content -->


        <mat-nav-list>

          <mat-list-item routerLink="/public/dashboard" *ngIf="userData.role_id == 'superadmin' || userData.role_id != 'superadmin'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>dashboard</mat-icon>
            <h3 matLine>Dashboard</h3>
          </mat-list-item>
          <mat-list-item routerLink="/public/anggota" *ngIf="userData.role_id == 'superadmin' || userData.role_id != 'superadmin'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>person</mat-icon>
            <h3 matLine>Anggota</h3>
          </mat-list-item>

          <mat-divider></mat-divider>
          <div mat-subheader >Database</div>
          <mat-list-item routerLink="/public/data-wilayah" *ngIf="userData.role_id == 'superadmin'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>chevron_right</mat-icon>
            <h3 matLine>Data Wilayah</h3>
          </mat-list-item>

          <mat-list-item routerLink="/public/data-daerah" *ngIf="userData.role_id == 'superadmin' || userData.role_id == 'admin_wilayah'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>chevron_right</mat-icon>
            <h3 matLine>Data Daerah</h3>
          </mat-list-item>

          <mat-list-item routerLink="/public/data-cabang" *ngIf="userData.role_id == 'superadmin' || userData.role_id == 'admin_wilayah' || userData.role_id == 'admin_daerah'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>chevron_right</mat-icon>
            <h3 matLine>Data Cabang</h3>
          </mat-list-item>

          <mat-list-item routerLink="/public/data-ranting" *ngIf="userData.role_id == 'superadmin' || userData.role_id == 'admin_wilayah' || userData.role_id == 'admin_daerah'" routerLinkActive="active-list-item">
            <mat-icon matListIcon>chevron_right</mat-icon>
            <h3 matLine>Data Ranting</h3>
          </mat-list-item>

          <div *ngIf="userData.role_id == 'superadmin'">
            <mat-divider></mat-divider>
            <div mat-subheader>Pengaturan</div>
            <mat-list-item routerLink="/public/pengguna" *ngIf="userData.role_id == 'superadmin'" routerLinkActive="active-list-item">
              <mat-icon matListIcon>account_circle</mat-icon>
              <h3 matLine>Pengguna</h3>
            </mat-list-item>
            <mat-list-item routerLink="/public/organisasi" *ngIf="userData.role_id == 'superadmin'" routerLinkActive="active-list-item">
              <mat-icon matListIcon>badge</mat-icon>
              <h3 matLine>KTA</h3>
            </mat-list-item>
          </div>

        </mat-nav-list>

      </div>

      <div style="position: absolute; bottom: 0; width: 100%;">
        <mat-nav-list>
          <mat-divider></mat-divider>
          <mat-list-item>
            <h3 matLine style="color: #2e7d32;">Aisyiyah App Version 1.0.0</h3>
          </mat-list-item>
          </mat-nav-list>
      </div>

      </mat-sidenav>

      <mat-sidenav-content>
       <router-outlet></router-outlet>
      </mat-sidenav-content>
  </mat-sidenav-container>

  </ng-container>
```

### public.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { PublicComponent } from './public.component';

describe('PublicComponent', () => {
  let component: PublicComponent;
  let fixture: ComponentFixture<PublicComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ PublicComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(PublicComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

### public.component.ts 

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';
import { ApiService } from '../services/api.service';
import { interval } from 'rxjs';

@Component({
  selector: 'app-public',
  templateUrl: './public.component.html',
  styleUrls: ['./public.component.scss']
})
export class PublicComponent implements OnInit {

  pageTitle:any;
  id:any;
  constructor(
    public router:Router,
    public api: ApiService,
    public routes: ActivatedRoute
  ) {
  }

  ngOnInit() {
    this.checkRole();
  }

  userData:any={};
  checkRole()
  {
    this.api.me().subscribe(res=>{
      this.userData=res;
    },err=>{
      this.router.navigate(['/login']);
    })
  }

  logout()
  {
    var conf=confirm('Keluar aplikasi?');
    if(conf)
    {
      localStorage.removeItem('ppaToken');
      location.reload();
    }
  }

}
```

### public.module.ts 

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { PublicComponent } from './public.component';
import { DashbordComponent } from './dashbord/dashbord.component';
import { Routes, RouterModule } from '@angular/router';

//Module
import {MatToolbarModule} from '@angular/material/toolbar';
import {MatSidenavModule} from '@angular/material/sidenav';
import {MatCardModule} from '@angular/material/card';
import {MatButtonModule} from '@angular/material/button';
import {MatIconModule} from '@angular/material/icon';
import {MatListModule} from '@angular/material/list';
import {MatGridListModule} from '@angular/material/grid-list';
import {MatChipsModule} from '@angular/material/chips';
import {MatDialogModule} from '@angular/material/dialog';
import {MatTableModule} from '@angular/material/table';
import {MatPaginatorModule} from '@angular/material/paginator';
import {MatFormFieldModule} from '@angular/material/form-field';
import {MatDatepickerModule} from '@angular/material/datepicker';
import {MatTabsModule} from '@angular/material/tabs';
import {MatInputModule} from '@angular/material/input';
import {MatMenuModule} from '@angular/material/menu';
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner';
import {MatRadioModule} from '@angular/material/radio'
import {MatSelectModule} from '@angular/material/select';
import {MatNativeDateModule} from '@angular/material/core';
import {MatSortModule} from '@angular/material/sort';
import {MatButtonToggleModule} from '@angular/material/button-toggle';

import { DataWilayahComponent, DialogWilayah } from './database/data-wilayah/data-wilayah.component';
import { DataDaerahComponent, DialogDaerah } from './database/data-daerah/data-daerah.component';
import { DataCabangComponent,  DialogCabang} from './database/data-cabang/data-cabang.component';
import { DataRantingComponent, DialogRanting } from './database/data-ranting/data-ranting.component';
import { DetailWilayahComponent } from './database/detail-wilayah/detail-wilayah.component';
import { DetailDaerahComponent } from './database/detail-daerah/detail-daerah.component';
import { DetailRantingComponent, DialogAnggota, DialogKartuAnggota } from './database/detail-ranting/detail-ranting.component';
import { DetailCabangComponent } from './database/detail-cabang/detail-cabang.component';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
// Import ngx-barcode module
import { NgxBarcodeModule } from 'ngx-barcode';
import { DialogHapusComponent } from './dialog-hapus/dialog-hapus.component';
import { MatAutocompleteModule } from '@angular/material/autocomplete';
import { MatCheckboxModule } from '@angular/material/checkbox';
import { PenggunaComponent } from './pengaturan/pengguna/pengguna/pengguna.component';
import { DialogUsersComponent } from './pengaturan/pengguna/dialog-users/dialog-users.component';
import { DialogPassAdminComponent } from './pengaturan/pengguna/dialog-pass-admin/dialog-pass-admin.component';
import { DialogDaerahComponent } from './database/dialog-daerah/dialog-daerah.component';
import { DialogWilayahComponent } from './database/dialog-wilayah/dialog-wilayah.component';
import { AnggotaComponent } from './anggota/anggota/anggota.component';
import { DaftarAnggotaComponent } from './anggota/template-daftar-anggota/daftar-anggota.component';
import { TemplateFormAnggotaComponent } from './anggota/template-form-anggota/template-form-anggota.component';
import { DialogDetailAnggotaComponent } from './anggota/dialog-detail-anggota/dialog-detail-anggota.component';
import { ImageUploaderComponent } from '../media/image-uploader/image-uploader.component';
import { ImageCropperComponent, ImageCropperModule } from 'ngx-image-cropper';
import { NgxPrintModule } from 'ngx-print';
import { OrganisasiComponent } from './pengaturan/organisasi/organisasi.component';
import { MatSnackBarModule } from '@angular/material/snack-bar';

const routes: Routes = [
  {
    path: '',
    component: PublicComponent,
    children: [
      {
        path: 'dashboard',
        component: DashbordComponent,
        data:{title:'Dashboard'}
      },
      {
        path: 'anggota',
        component: AnggotaComponent,
        data:{title:'Pengelolaan Anggota'}
      },
      {
        path: 'pengguna',
        component: PenggunaComponent,
        data:{title:'pengguna'}
      },
      {
        path: 'organisasi',
        component: OrganisasiComponent,
        data:{title:'Pengaturan Organisasi'}
      },
      {
        path: 'data-wilayah',
        component: DataWilayahComponent,
        data:{title:'Data Wilayah'}
      },
      {
        path: 'data-daerah',
        component: DataDaerahComponent,
        data:{title:'Data Daerah'}
      },
      {
        path: 'data-cabang',
        component: DataCabangComponent,
        data:{title:'Data Cabang'}
      },
      {
        path: 'data-ranting',
        component: DataRantingComponent,
        data:{title:'Data Ranting'}
      },
      {
        path: 'detail-wilayah/:id',
        component: DetailWilayahComponent,
        data:{title:'Nama Wilayah'}
      },
      {
        path: 'detail-daerah/:id',
        component: DetailDaerahComponent,
        data:{title:'Nama Daerah'}
      },
      {
        path: 'detail-cabang/:id',
        component: DetailCabangComponent,
        data:{title:'Nama Cabang'}
      },
      {
        path: 'detail-ranting/:id',
        component: DetailRantingComponent,
        data:{title:'Nama Ranting'}
      },
    ]
  },
  {
    path: '',
    redirectTo: '/public/dashboard',
    pathMatch: 'full'
  }
]

@NgModule({
  declarations: [
    PublicComponent,
    DashbordComponent,
    DataWilayahComponent,
    DataDaerahComponent,
    DataCabangComponent,
    DataRantingComponent,
    DetailWilayahComponent,
    DialogWilayah,
    DetailDaerahComponent,
    DialogDaerah,
    DetailRantingComponent,
    DialogRanting,
    DetailCabangComponent,
    DialogCabang,
    DialogAnggota,
    DialogKartuAnggota,    
    DialogHapusComponent,
    PenggunaComponent,
    DialogUsersComponent,
    DialogPassAdminComponent,
    DialogDaerahComponent,
    DialogWilayahComponent,
    AnggotaComponent,
    DaftarAnggotaComponent,
    TemplateFormAnggotaComponent,
    DialogDetailAnggotaComponent,
    ImageUploaderComponent,
    OrganisasiComponent
  ],
  imports: [
    CommonModule,
    RouterModule.forChild(routes),
    MatToolbarModule,
    MatSidenavModule,
    RouterModule,
    MatMenuModule,
    MatCardModule,
    MatButtonModule,
    MatIconModule,
    MatListModule,
    MatGridListModule,
    MatChipsModule,
    MatDialogModule,
    MatTableModule,
    MatPaginatorModule,
    MatFormFieldModule,
    MatDatepickerModule,
    MatTabsModule,
    MatInputModule,
    MatSelectModule,
    MatNativeDateModule,
    MatSortModule,
    MatAutocompleteModule,
    MatCheckboxModule,
    FormsModule,
    ReactiveFormsModule,
    MatProgressSpinnerModule,
    MatButtonToggleModule,
    NgxBarcodeModule,
    MatRadioModule,
    ImageCropperModule,
    NgxPrintModule,
    MatSnackBarModule
  ],
  entryComponents: [
    DialogWilayah,
    DialogDaerah,
    DialogRanting,
    DialogCabang,
    DialogAnggota,
    DialogHapusComponent,
    DialogUsersComponent,
    DialogPassAdminComponent   
  ],
})
export class SchoolModule { }
```

### Anggota

#### Anggota

##### anggota.component.html

```html
<h1 style="padding: 30px; margin: 0px">Pengelolaan Anggota</h1>

<!--TAB MENU-->
<nav mat-tab-nav-bar>
  <div
    mat-tab-link
    *ngFor="
      let link of [
        'Tambah Anggota',
        'Pencarian Anggota',
        'Daftar Anggota'       
      ];
      let idx = index
    "
    (click)="activeTab = idx"
    [active]="activeTab == idx"
  >
    {{ link }}
  </div>
</nav>

<!--END TAB MENU-->

<!--DAFTAR ANGGOTA-->
<section *ngIf="activeTab == 2">
 <div style="padding: 5px 5px 5px 20px">
    <button mat-icon-button mat-button [matMenuTriggerFor]="menuKategori">
      <mat-icon>filter_list</mat-icon>
    </button>
    <mat-menu #menuKategori="matMenu">
      <button mat-menu-item (click)="getFilterCategory(0)">
        Data terakhir
      </button>   
      <button mat-menu-item *ngIf="userData.role_id == 'superadmin'" (click)="getFilterCategory(5)">
        Data berdasarkan status
      </button>  
      <button mat-menu-item *ngIf="userData.role_id == 'superadmin'" (click)="getFilterCategory(1)">
        Data berdasarkan wilayah
      </button>
      <button mat-menu-item *ngIf="userData.role_id == 'superadmin' || userData.role_id == 'admin_wilayah'" (click)="getFilterCategory(2)">
        Data berdasarkan daerah
      </button>
      <button mat-menu-item (click)="getFilterCategory(3)">
        Data berdasarkan cabang
      </button>
      <button mat-menu-item (click)="getFilterCategory(4)">
        Data berdasarkan ranting
      </button>
    </mat-menu>

    <span class="filter-type" style="padding-left: 25px">{{filter_types[filter_type_selected]}}</span>

   <!--FILTER: LAST DATA-->
    <div
      *ngIf="filter_type_selected == 0"
      style="display: inline-block; margin-left: 10px"
      
    >
    <div class="uk-flex uk-fex-start uk-flex-middle">
      <input class="uk-input" style="width: 80px;" [(ngModel)]="limit" (keyup.enter)="getDataAnggota()" type="number" placeholder="Jumlah data.." />
      <button mat-button (click)="getDataAnggota()" [disabled]="loadingDataAnggota">{{ loadingDataAnggota ? 'Sedang mengambil data..':'Tampilkan Data'}}</button>
    </div>
   
  </div>
    <!--END FILTER LAST DATA-->
    <!--FILTER: ORG DATA-->
    <div
      *ngIf="filter_type_selected > 0 && filter_type_selected < 5"
      style="display: inline-block; margin-left: 10px"
    >
      <mat-chip-list #chipList aria-label="Fruit selection">
        <mat-chip
          *ngFor="let fruit of fruits"
          [removable]="true"
          (removed)="remove(fruit)"
        >
          {{ fruit.nama }}
          <mat-icon matChipRemove>cancel</mat-icon>
        </mat-chip>
        <input
          placeholder="Tulis nama {{ filter_types[filter_type_selected] }}..."
          (keyup)="searchFilterData($event)"
          #fruitInput
          [formControl]="fruitCtrl"
          [matAutocomplete]="auto"
          [matChipInputFor]="chipList"
          [matChipInputSeparatorKeyCodes]="separatorKeysCodes"
          (matChipInputTokenEnd)="add($event)"
        />
      </mat-chip-list>
      <mat-autocomplete
        [panelWidth]="250"
        #auto="matAutocomplete"
        (optionSelected)="selected($event)"
      >
        <mat-option
          *ngFor="let fruit of filteredFruits | async"
          [value]="fruit"
        >
          {{ fruit.nama }}
        </mat-option>
      </mat-autocomplete>
    </div>

    <div
      *ngIf="filter_type_selected == 5"
      style="display: inline-block; margin-left: 10px">

      <mat-form-field class="uk-width-1-1" appearance="outline">
        <mat-label>Pilih Status Keanggotaan</mat-label>
        <mat-select [(ngModel)]="status" (ngModelChange)="searchFilterByStatus()">
          <mat-option value="1">Disetujui</mat-option>
          <mat-option value="0">Belum Disetujui</mat-option>
        </mat-select>
      </mat-form-field>
    </div>
    <!--END FILTER ORG DATA-->

  <!--DATA ACTIONS-->
  <!--
<mat-toolbar *ngIf="selection.selected.length > 0">
    <span style="margin-left: 10px;">{{selection.selected.length}} Data Terpilih</span>
    <button mat-button (click)="exportexcel()">Export Data</button>
    <button mat-button (click)="hapusSelAnggota()">Hapus Data</button>
    <div class="spacer"></div>
    <button mat-icon-button mat-button (click)="closeSelected()">
        <mat-icon>close</mat-icon>
    </button>
</mat-toolbar>
-->
</div>
  <!--END DATA ACTIONS-->
  <tabel-anggota [dataAnggota]="exportedDataAnggota" [loadingStatus]="loadingDataAnggota"></tabel-anggota>
  
  <div class="uk-flex uk-flex-center" style="padding: 30px 0 30px 0;">
    <button mat-flat-button (click)="getDataAnggota()" [disabled]="loadingDataAnggota">{{ loadingDataAnggota ? 'Sedang mengambil data..':'Tampilkan Data Berikutnya'}}</button>
  </div>
</section>
<!--END DAFTAR ANGGOTA-->

<mat-card>
<!-- PENCARIAN -->
  <section *ngIf="activeTab == 1">
    <div class="uk-margin-top">
      <form class="uk-search uk-search-default uk-width-1-1">
        <span uk-search-icon></span>
        <input
          class="uk-search-input"
          type="search"
          [(ngModel)]="searchValue"
          name="value_cari"
          (keydown.enter)="searchMember()"
          placeholder="Masukan nama atau nomor anggota lalu tekan ENTER..."
        />
      </form>
    </div>
    <div *ngIf="searchResults.length > 0">
      <tabel-anggota [dataAnggota]="searchResultDataSource"></tabel-anggota>    
    </div>
  </section>
<!--END PENCARIAN-->

<!--TAB: TAMBAH ANGGOTA -->
  <section *ngIf="activeTab == 0">
    <div style="margin-top: 20px"></div>
    <form-anggota [buttonText]="'Simpan'"></form-anggota>   
  </section>
<!--TAB: IMPORT DATA-->
  <section *ngIf="activeTab == 3">
    <input
      type="file"
      id="file"
      (change)="onFileChange($event)"
      #fileInput
      style="display: none"
    />

    <div class="uk-flex uk-margin uk-flex-middle uk-text-center">
      <div class="uk-width-expand" style="margin-right: 10px">
        <div
          style="padding: 5px; outline: 2px dashed #ccc; text-align: center"
          (click)="fileInput.click()"
        >
          {{ selectedFile ? selectedFile.name : "Pilih File" }}
        </div>
      </div>
      <!-- <div>
        <button class="uk-button uk-button-primary ">UPLOAD</button>
      </div> -->
    </div>

    <div class="uk-alert alert-info uk-alert-success">
      Contoh format data excel yang dapat diproses
      <a href="assets/format_import_anggota.xlsx">unduh disini</a><br />
    </div>
    <div class="uk-alert alert-info uk-alert-danger">
      Pilih file excel (.xlsx) kemudian klik upload. Periksa kembali data yang
      akan dimasukkan kedalam database. Pastikan data sudah benar kemudian tekan
      tombol Simpan untuk memasukkan data kedalam database
    </div>

    <table
      mat-table
      matSort
      [dataSource]="dataSourceImport"
      (matSortChange)="sortData($event)"
      class="anggota-table"
    >
      <ng-container matColumnDef="no">
        <th mat-sort-header="no" mat-header-cell *matHeaderCellDef>No.</th>
        <td mat-cell *matCellDef="let i = index">
          {{
            this.paginator.pageIndex == 0
              ? i + 1
              : 1 + i + this.paginator.pageIndex * this.paginator.pageSize
          }}
        </td>
      </ng-container>

      <ng-container matColumnDef="no_ktaa">
        <th mat-sort-header="no_ktaa" mat-header-cell *matHeaderCellDef>
          No KTAA
        </th>
        <td mat-cell *matCellDef="let element">{{ element.nba }}</td>
      </ng-container>

      <ng-container matColumnDef="nama_anggota">
        <th mat-sort-header="nama_anggota" mat-header-cell *matHeaderCellDef>
          Nama Anggota
        </th>
        <td mat-cell *matCellDef="let element">{{ element.nama }}</td>
      </ng-container>

      <ng-container matColumnDef="tempat_lahir">
        <th mat-sort-header="tempat_lahir" mat-header-cell *matHeaderCellDef>
          Tempat Lahir
        </th>
        <td mat-cell *matCellDef="let element">{{ element.tempat_lahir }}</td>
      </ng-container>

      <ng-container matColumnDef="tanggal">
        <th mat-sort-header="tanggal" mat-header-cell *matHeaderCellDef>
          Tanggal Lahir
        </th>
        <td mat-cell *matCellDef="let element">
          {{ element.tanggal_lahir | date: "dd/MM/yyyy" }}
        </td>
      </ng-container>

      <ng-container matColumnDef="wilayah">
        <th mat-sort-header="wilayah" mat-header-cell *matHeaderCellDef>
          Wilayah
        </th>
        <td mat-cell *matCellDef="let element">{{ element.wilayah }}</td>
      </ng-container>

      <ng-container matColumnDef="daerah">
        <th mat-sort-header="daerah" mat-header-cell *matHeaderCellDef>
          Daerah
        </th>
        <td mat-cell *matCellDef="let element">{{ element.daerah }}</td>
      </ng-container>

      <ng-container matColumnDef="cabang">
        <th mat-sort-header="cabang" mat-header-cell *matHeaderCellDef>
          Cabang
        </th>
        <td mat-cell *matCellDef="let element">{{ element.cabang }}</td>
      </ng-container>

      <ng-container matColumnDef="ranting">
        <th mat-sort-header="ranting" mat-header-cell *matHeaderCellDef>
          Ranting
        </th>
        <td mat-cell *matCellDef="let element">{{ element.ranting }}</td>
      </ng-container>

      <!-- <ng-container matColumnDef="#">
          <th mat-header-cell *matHeaderCellDef> # </th>
          <td mat-cell *matCellDef="let element; let i=index">
              <button mat-icon-button [matMenuTriggerFor]="menu">
                  <mat-icon>more_vert</mat-icon>
              </button>
              <mat-menu #menu="matMenu">
                <button mat-menu-item (click)="dialogAnggota(element)" ><mat-icon>create</mat-icon>Perbarui Data Anggota</button>
              </mat-menu>
          </td>
      </ng-container> -->
      <tr mat-header-row *matHeaderRowDef="displayedColumn3; sticky: true"></tr>
      <tr mat-row *matRowDef="let row; columns: displayedColumn3"></tr>
    </table>
    <mat-paginator
      #paginator3
      [pageSizeOptions]="[10, 100, 500]"
      showFirstLastButtons
    ></mat-paginator>
    <div class="uk-grid-small" uk-grid>
      <div class="uk-width-expand"></div>
      <div>
        <button
          mat-flat-button
          color="primary"
          [disabled]="dataImport.length == 0"
        >
          Simpan Data
        </button>
      </div>
    </div>
  </section>

</mat-card>
```

##### anggota.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { AnggotaComponent } from './anggota.component';describe('AnggotaComponent', () => {  let component: AnggotaComponent;  let fixture: ComponentFixture<AnggotaComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ AnggotaComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(AnggotaComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### anggota.component.ts

```typescript
import { Component, ElementRef, OnInit, ViewChild } from '@angular/core';
import { MatDialog } from '@angular/material/dialog';
import { ImageUploaderComponent } from 'src/app/media/image-uploader/image-uploader.component';
import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';
import { ApiService } from 'src/app/services/api.service';
import { Sort } from '@angular/material/sort';
import { MatTableDataSource } from '@angular/material/table';
import { MatPaginator } from '@angular/material/paginator';
import { DialogHapusComponent } from '../../dialog-hapus/dialog-hapus.component';
import { SelectionModel } from '@angular/cdk/collections';
import * as XLSX from 'xlsx';
import {
  MatAutocomplete,
  MatAutocompleteSelectedEvent,
} from '@angular/material/autocomplete';
import { DateAdapter } from '@angular/material/core';
import * as moment from 'moment';
import { COMMA, ENTER } from '@angular/cdk/keycodes';
import { FormControl } from '@angular/forms';
import { Observable } from 'rxjs';
import { MatChipInputEvent } from '@angular/material/chips';
import { map, startWith } from 'rxjs/operators';
import { DaftarAnggotaComponent } from '../template-daftar-anggota/daftar-anggota.component';
import { TemplateFormAnggotaComponent } from '../template-form-anggota/template-form-anggota.component';
@Component({
  selector: 'app-anggota',
  templateUrl: './anggota.component.html',
  styleUrls: ['./anggota.component.scss'],
})
export class AnggotaComponent implements OnInit {
  activeTab: any = 0;
  dataAnggota: any = {};
  searchValue: any;
  searchResults: any = [];
  userData: any = {};
  dataImport: any = [];
  dataWilayah: any = [];
  dataDaerah: any = [];
  dataCabang: any = [];
  dataRanting: any = [];
  constructor(private api: ApiService) {
    this.chipConstructor();
  }

  ngOnInit(): void {
    this.getUserData();
    this.getDataOrg();    
  }

  getUserData()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData = res;
      this.checkUserRole();
    });
  }

  async getDataOrg()
  {
    this.dataWilayah = await this.api.get('org/wilayah').toPromise();
    this.dataDaerah = await this.api.get('org/daerah').toPromise();   
    this.getDataAnggota();
  }

  checkUserRole()
  {
    if(this.userData.role_id == 'admin_wilayah')
    {
      this.dataAnggota.wilayah = this.userData.area_id;
    }
    if(this.userData.role_id == 'admin_daerah')
    {      
      let arr = this.userData.area_id;
      this.dataAnggota.wilayah = arr.substring(0,2);
      this.dataAnggota.daerah = arr.substring(0,4);
    }
  }


  //TAB: SEARCH MEMBER
  searchResultDataSource: any = [];
  searchTableColumns: string[] = [
    'nba',
    'nama',
    'wilayah',
    'daerah',
    'cabang',
    'ranting',
    'disetujui',
    '#',
  ];
  searchMember() {
    if(this.searchValue.includes('.')) {
      this.api.get('anggota?key_nba=' + this.searchValue).subscribe(
        (res) => {
          this.searchResults = res;
          this.searchResultDataSource = new MatTableDataSource(this.searchResults);
          this.searchValue = '';
        },
        (error) => {
          return;
        }
      );
    } else {
      this.api.get('anggota?key_nama=' + this.searchValue).subscribe(
        (res) => {
          this.searchResults = res;
          this.searchResultDataSource = new MatTableDataSource(this.searchResults);
          this.searchValue = '';
        },
        (error) => {
          return;
        }
      );

    }
  }
  applySearchResultFilter(event: Event) {
    const filterValue = (event.target as HTMLInputElement).value;
    this.searchResultDataSource.filter = filterValue.trim().toLowerCase();
  }
  //END : TAB SEARCH MEMBER

  //TAB: DAFTAR ANGGOTA
  //categories
  dataFilterCategory: any = [];
  filterSelected: any = [];
  filterSelectedID: any = [];
  filter_types: any = [
    'Data terakhir',
    'Wilayah',
    'Daerah',
    'Cabang',
    'Ranting',
    'Status',
  ];
  filter_type_selected: any = 0;  
  getFilterCategory(id) {
    this.filter_type_selected = id;
    this.fruits = [];
    if(id == 0) this.getDataAnggota();
  }
  //chip & autocomplete
  selectable = true;
  removable = true;
  separatorKeysCodes: number[] = [ENTER, COMMA];
  fruitCtrl = new FormControl();
  filteredFruits: Observable<string[]>;
  fruits: any = [];
  allFruits: any = [];

  @ViewChild('fruitInput') fruitInput: ElementRef<HTMLInputElement>;

  chipConstructor() {
    this.filteredFruits = this.fruitCtrl.valueChanges.pipe(
      startWith(''),
      map((fruit: any) =>
        fruit ? this._filter(fruit) : this.allFruits.slice()
      )
    );
  }

  add(event: MatChipInputEvent): void {
    const value = (event.value || '').trim();
    // Add our fruit
    if (value) {
      this.fruits.push(value);
    }
    // Clear the input value
    this.fruitCtrl.setValue(null);
  }

  remove(fruit: string): void {
    const index = this.fruits.indexOf(fruit);
    if (index >= 0) {
      this.fruits.splice(index, 1);
    }
    this.getDataAnggota();
  }

  selected(event: MatAutocompleteSelectedEvent): void {
    this.fruits.push(event.option.value);
    this.fruitInput.nativeElement.value = '';
    this.allFruits = [];    
    this.fruitCtrl.setValue('');
    this.getDataAnggota();
  }

  private _filter(value): any {
    return this.allFruits.filter((data) =>
      data.nama.toLowerCase().includes(value)
    );
  }
  searchFilterData(event) {
    let category = this.filter_types[this.filter_type_selected].toLowerCase();
    let key = this.fruitCtrl.value;
    this.api
      .get('org/search?category=' + category + '&key=' + key)
      .subscribe((res) => {
        this.allFruits = res;
        this.chipConstructor();
      });
  }
  daftarAnggota: any = [];
  limit: any = 10; 
  lastFilterSelected: any;
  loadingDataAnggota: boolean = false;
  async getDataAnggota()
  {        
    let params = '';
    let keys = [];
    let key;    
    if(this.filter_type_selected != this.lastFilterSelected)
    {      
      this.daftarAnggota = [];
    }
    if(this.filter_type_selected == 0){
      params = 'limit='+this.limit+'&offset='+this.daftarAnggota.length;
    }else{
      if(this.filter_type_selected == 1) key = 'wilayah';
      if(this.filter_type_selected == 2) key = 'daerah';
      if(this.filter_type_selected == 3) key = 'cabang';
      if(this.filter_type_selected == 4) key = 'ranting';
      for(let item of this.fruits)
      {
        keys.push(item['id_'+key]);
      }
      if(this.fruits.length > 0) params = key+'='+keys.join(",")+'&limit='+this.limit+'&offset='+this.daftarAnggota.length;
    }
    this.loadingDataAnggota = true;
    this.api.get('anggota?'+params).subscribe(res=>{
      this.parseDataAnggota(res);      
      this.lastFilterSelected = this.filter_type_selected;
      this.loadingDataAnggota = false;
    },err=>{
      alert('Ada masalah saat mengambil data dari server.')
      this.loadingDataAnggota = false;
    });
  }

  status = 0;
  searchFilterByStatus() {
    this.exportedDataAnggota = [];
    this.daftarAnggota = [];
    let params = '';
    params = 'disetujui='+this.status+'&limit='+this.limit+'&offset='+this.daftarAnggota.length;

    this.loadingDataAnggota = true;
    this.api.get('anggota?'+params).subscribe(res=>{
      this.parseDataAnggota(res);      
      this.lastFilterSelected = this.filter_type_selected;
      this.loadingDataAnggota = false;
    },err=>{
      alert('Ada masalah saat mengambil data dari server.')
      this.loadingDataAnggota = false;
    });
  }

  exportedDataAnggota: any = [];
  parseDataAnggota(data)
  {
    for(let dt of data)
    {
      // let idx_wilayah = this.dataWilayah.map(e=>{return e.id_wilayah}).indexOf(dt.wilayah);
      // let idx_daerah = this.dataDaerah.map(e=>{return e.id_daerah}).indexOf(dt.daerah);
      // dt.nama_wilayah = this.dataWilayah[idx_wilayah].nama;
      // dt.nama_daerah = this.dataDaerah[idx_daerah].nama;      
      this.daftarAnggota.push(dt);
    } 
    this.exportedDataAnggota = new MatTableDataSource(this.daftarAnggota);
  }
  
  

  //END: DAFTAR ANGGOTA
}
```

#### Dialog Detail Anggota

##### dialog-detail-anggota.component.html

```html
<div mat-dialog-content>    <div class="uk-flex uk-flex-right">        <button mat-raised-button color="primary" (click)="getKtaData()">            Sinkronisasi Data Konfigurasi KTAA        </button>        <button mat-icon-button mat-dialog-close>            <mat-icon>close</mat-icon>        </button>    </div>    <div class="uk-flex uk-flex-around uk-flex-middle uk-margin" style="padding: 30px; background-color: #ccc;">        <div style="text-align: center; width: 380px;        height: 235px;">            <div            style="width: 380px;            height: 235px;            border-radius: 5px;            background-color: white;            position: relative;" id="print-section">                <div                style="position: absolute;                left: 20px;                right: 20px;                top: 5px;                text-align: center;">                    <h2  style="font-size: 14px;                    line-height: 18px;                    font-weight: 600;                    padding: 0;                    padding-bottom: 1px;                    margin: 0;                    color: black;                    text-align: center;">KARTU TANDA ANGGOTA 'AISYIYAH</h2>                    <h4  style="font-size: 12px;                    line-height: 12px;                    font-weight: 600;                    padding: 0;                    margin: 0;                    color: darkblue;                    text-align: center;">{{dataAnggota.nba}}</h4>                </div>                <div class="photo" style="position: absolute;                top: 45px;                left: 15px;                width: 80px;                height: 80px;                background-color: #ccc;                border-radius: 5px;                cursor: pointer;" (click)="openMedia()">                    <img src="{{dataAnggota.avatar_data}}" style="border-radius: 5px;                    width: 70px;" id="member-photo">                    <h4 class="nba-last" style="font-size: 12px;                    line-height: 12px;                    font-weight: 600;                    padding: 0;                    margin: 0;                    margin-top: 15px;                    color: darkblue;                    text-align: center;">{{dataAnggota.last_nba}}</h4>                </div>                <div style="position: absolute;left: 105px;top: 39px;right: 2px;text-align: left;">                       <table style="border-collapse:collapse;                       padding:0px;                       font-size: 9px;                       line-height: 9px;                       margin: 0px;                       font-weight: 530;                       padding-left: 0px;                       vertical-align: top;                       color: black;">                           <tr cellpadding="0px">                               <td>Nama</td>                               <td>:</td>                               <td>{{dataAnggota.nama}}</td>                           </tr>                           <tr>                            <td>Tempat/Tgl.Lahir</td>                            <td>:</td>                            <td>{{dataAnggota.tempat_lahir}}, {{dataAnggota.tanggal_lahir | date:'dd-MM-y' }}</td>                        </tr>                        <tr>                            <td>Pekerjaan</td>                            <td>:</td>                            <td>{{dataAnggota.pekerjaan}}</td>                        </tr>                        <tr>                            <td>Alamat</td>                            <td>:</td>                            <td *ngIf="printFullAddress">                                RT {{dataAnggota.rt}} RW {{dataAnggota.rw}},                                Kel. {{orgDetail[dataAnggota.ranting] == undefined ? dataAnggota.ranting:transform(orgDetail[dataAnggota.ranting].nama)}}, Kec. {{orgDetail[dataAnggota.cabang] == undefined ? dataAnggota.cabang:transform(orgDetail[dataAnggota.cabang].nama)}} <br>                                Kab/kota {{orgDetail[dataAnggota.daerah] == undefined ? dataAnggota.daerah:transform(orgDetail[dataAnggota.daerah].nama)}},                                Prov. {{orgDetail[dataAnggota.wilayah] == undefined ? dataAnggota.wilayah:transform(orgDetail[dataAnggota.wilayah].nama)}}                            </td>                            <td *ngIf="!printFullAddress">                                {{dataAnggota.alamat}}                            </td>                        </tr>                        <tr>                            <td>NBM</td>                            <td>:</td>                            <td>{{dataAnggota.nbm}}</td>                        </tr>                        <tr>                            <td>Ranting</td>                            <td>:</td>                            <td>{{ orgDetail[dataAnggota.ranting] == undefined ? '': transform(orgDetail[dataAnggota.ranting].nama)}}</td>                        </tr>                        <tr>                            <td>Cabang</td>                            <td>:</td>                            <td>{{ orgDetail[dataAnggota.cabang] == undefined ? '': transform(orgDetail[dataAnggota.cabang].nama) }}</td>                        </tr>                        <tr>                            <td>Daerah</td>                            <td>:</td>                            <td>{{ orgDetail[dataAnggota.daerah] == undefined ? '' : transform(orgDetail[dataAnggota.daerah].nama) }}</td>                        </tr>                        <tr>                            <td>Wilayah</td>                            <td>:</td>                            <td>{{ orgDetail[dataAnggota.wilayah] == undefined ? '': transform(orgDetail[dataAnggota.wilayah].nama) }}</td>                        </tr>                       </table>                </div>                <img class="signature-ketua" style="position: absolute;                bottom: 20px;                left: 20%;                width: 77px;" src="{{ktaData.ttd_ketua_dataurl}}">                <img class="signature-sekretaris" style="position: absolute;                bottom: 10px;                right: 15%;                width: 55px;" src="{{ktaData.ttd_sekretaris_dataurl}}">                <div style=" position: absolute;                bottom: 20px;                right: -40px;                width: 100%;                text-align: center;                " >                <img style="width: 50px;" src="{{ktaData.stampel_dataurl}}">                </div>                <div class="signature uk-text-center" style="font-size: 9px;                line-height: 10px;                font-weight: 500;                position: absolute;                bottom: 3px;                left: 10px;                right: 10px;                color: black;                ">                    <p style="padding: 0px;                    margin: 0px; text-align: center;">Yogyakarta, {{dataAnggota.created_at | date: 'dd/MM/yyyy'}}</p>                    <p style="padding: 0px;                    margin: 0px; text-align: center;">Pimpinan Pusat 'Aisyiyah</p>                    <table style="width: 100%; margin-top: 0px; font-size: 9px;                    line-height: 9px; text-align: center;">                        <tr style="text-align: center;">                            <td>Ketua Umum</td>                            <td>Sekretaris Umum</td>                        </tr>                        <tr style="height: 20px;">                            <td></td>                            <td></td>                        </tr>                        <tr>                            <td><b>{{ktaData.nama_ketua}}</b><br>{{ktaData.nba_ketua}} </td>                            <td><b>{{ktaData.nama_sekretaris}}</b> <br>{{ktaData.nba_sekretaris}}</td>                        </tr>                    </table>                </div>             </div>        </div>        <p style="text-align: center;">Klik area foto untuk menambah atau mengganti foto.</p>    </div>    <div style="text-align: center;" *ngIf="dataAnggota.disetujui == 1">    <span>NBA:</span>    <h1>{{dataAnggota.nba}}</h1>    </div>    <div style="border: 1px #ccc solid; border-radius: 5px;" class="uk-margin">      <mat-list>          <mat-list-item>              <h3 matLine>Validasi anggota</h3>              <p matLine *ngIf="dataAnggota.disetujui == 0">                  <span >Belum tervalidasi. Validasi dilakukan oleh admin PP Aisyiyah.</span>              </p>              <h3 matLine *ngIf="dataAnggota.disetujui == 1">                Data anggota sudah divalidasi              </h3>              <button *ngIf="userData.role_id == 'superadmin' && dataAnggota.disetujui == 0" mat-raised-button color="primary" (click)="validasi()" [disabled]="loadingValidasi">{{loadingValidasi ? 'Memvalidasi..':'Validasi'}}</button>              <mat-icon color="primary" *ngIf="dataAnggota.disetujui == 1">                  verified_user              </mat-icon>              <mat-icon color="accent" *ngIf="dataAnggota.disetujui == 0 && userData.role_id != 'superadmin'">                  error_outline              </mat-icon>              <mat-divider></mat-divider>          </mat-list-item>          <mat-list-item *ngIf="userData.role_id == 'superadmin' && dataAnggota.disetujui == 1">           <h3 matLine>Tampilkan alamat lengkap</h3>            <mat-checkbox class="example-margin" [(ngModel)]="printFullAddress"></mat-checkbox>            <mat-divider></mat-divider>        </mat-list-item>          <mat-list-item *ngIf="userData.role_id == 'superadmin'">              <h3 matLine>Cetak kartu anggota</h3>              <p matLine>                  <span *ngIf="dataAnggota.disetujui == 0">Cetak kartu hanya untuk anggota yang valid. </span>                  <span>Terakhir cetak pada {{dataAnggota.printed_at}}</span>              </p>              <button mat-raised-button color="primary" printSectionId="print-section" ngxPrint [disabled]="loadingPrint || dataAnggota.disetujui == 0">{{loadingPrint ? 'Mencetak..':'Cetak Kartu'}}</button>              <mat-divider></mat-divider>          </mat-list-item>      </mat-list></div><mat-tab-group mat-align-tabs="center">  <mat-tab label="Data Anggota">    <div style="margin-top: 12px"></div>    <form-anggota [dataAnggota]="dataAnggota" [buttonText]="'Perbarui Data'"></form-anggota>  </mat-tab>  <mat-tab label="Pembayaran">    <div style="overflow-x: hidden; margin-top: 12px">      <mat-chip-list aria-label="Fish selection">        <mat-chip color="{{dataAnggota.id_payment == null ? 'accent':'primary'}}" selected>{{dataAnggota.id_payment == null ? 'Belum Bayar':'Sudah Bayar'}}</mat-chip>      </mat-chip-list>      <mat-form-field class="uk-width-1-1 uk-margin-top" appearance="outline">        <mat-label>Jumlah Bayar</mat-label>        <input [(ngModel)]="paymentData.amount" matInput [disabled]="dataAnggota.amount != null" required/>        <span *ngIf="paymentData.amount == undefined">Tambahkan jumlah bayar!</span>      </mat-form-field>      <p *ngIf="dataAnggota.id_payment != null"><i>Pembayaran berhasil dilakukan pada {{dataAnggota.created_at}}</i></p>        <p>Bukti Pembayaran</p>                <div style="text-align: left" *ngIf="dataAnggota.receipt_url != null" class="uk-width-1-3">            <img src="{{api.fileBaseUrl+dataAnggota.receipt_url}}" style="width: 100px"/>        </div>        <mat-form-field  class="uk-width-1-1" *ngIf="dataAnggota.id_payment == null" >            <div>            <mat-toolbar style="font-size: 14px;">                <!-- Readonly Input to show File names -->                <input matInput [(ngModel)]="myfilename" readonly name="memberContactNo" />                <!-- Browse Button -->                <button mat-flat-button color="primary">                Browse                </button>            </mat-toolbar>            <!-- Fetch selected filed on change -->            <input type="file" [disabled]="paymentData.amount == undefined" #UploadFileInput id="fileUpload" (change)="fileChangeEvent($event)" name="fileUpload"                multiple="multiple" accept="image/*" />            </div>        </mat-form-field>        <div class="uk-grid-small" uk-grid *ngIf="dataAnggota.id_payment == null">            <div class="uk-width-expand"></div>            <div>                <button mat-raised-button color="primary" (click)="konfirmasiPayment()" [disabled]="loading || paymentData.amount == undefined">{{loading ? 'Mohon Tunggu..':'Konfirmasi'}}</button>            </div>        </div>    </div>  </mat-tab></mat-tab-group></div>
```

##### dialog-detail-anggota.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DialogDetailAnggotaComponent } from './dialog-detail-anggota.component';describe('DialogDetailAnggotaComponent', () => {  let component: DialogDetailAnggotaComponent;  let fixture: ComponentFixture<DialogDetailAnggotaComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DialogDetailAnggotaComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DialogDetailAnggotaComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### dialog-detail-anggota.component.ts

```typescript
import { Component, ElementRef, Inject, OnInit, ViewChild } from '@angular/core';import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { ImageUploaderComponent } from 'src/app/media/image-uploader/image-uploader.component';import { ApiService } from 'src/app/services/api.service';import * as htmlToImage from 'html-to-image';import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image';import domtoimage from 'dom-to-image';import { MatSnackBar } from '@angular/material/snack-bar';@Component({  selector: 'app-dialog-detail-anggota',  templateUrl: './dialog-detail-anggota.component.html',  styleUrls: ['./dialog-detail-anggota.component.scss']})export class DialogDetailAnggotaComponent implements OnInit {  @ViewChild('UploadFileInput') uploadFileInput: ElementRef;  myfilename = 'Unggah Bukti';  dataAnggota: any = {};  userData: any = {};  photoBaseUrl: any;  orgDetail: any = {};  dataProvinsi: any = {};  dataKabupaten: any = {};  dataKecamatan: any = {};  dataKelurahan: any = {};  dataWilayah: any = {};  dataDaerah: any = {};  dataCabang: any = {};  dataRanting: any = {};  loading:boolean = false;  printFullAddress: boolean = false;  urlResize = 'https://api.sunhouse.co.id/cms/index.php/image/resize2?w=150&url=';  id:any;  constructor(    private dialogRef: MatDialogRef<DialogDetailAnggotaComponent>,    @Inject(MAT_DIALOG_DATA) public dialogData: any,    private dialog: MatDialog,    private api: ApiService,    private snackbar: MatSnackBar  )  {    this.loading = true;    this.photoBaseUrl = this.api.photoBaseUrl;    this.id = dialogData.dataAnggota.id;    this.getDetailAnggota();  }  ngOnInit(): void {    this.getUserData();    this.ktaData = JSON.parse(localStorage.getItem('ktaData'));    if(this.ktaData == null) {      this.getKtaData();    }  }  ktaData: any = {};  getKtaData()  {    this.api.get('config/kta').subscribe(res=>{      this.ktaData = res;      localStorage.setItem('ktaData', JSON.stringify(this.ktaData));      this.snackbar.open('Berhasil mensinkronisasikan data konfigurasi KTAA', 'Tutup', {duration: 2500});    })  }  getUserData()  {    this.api.get('users/me').subscribe(res=>{      this.userData = res;    })  }  getDetailAnggota()  {    this.api.get('anggota/find/'+this.id).subscribe(res=>{      this.dataAnggota = res;      if(this.dataAnggota.amount != null) {        this.paymentData.amount = this.dataAnggota.amount;      }      if(this.dataAnggota.wilayah != undefined && this.dataAnggota.wilayah != '') {        this.getOrgDetail('wilayah', this.dataAnggota.wilayah);      }      if(this.dataAnggota.daerah != undefined && this.dataAnggota.daerah != '') {        this.getOrgDetail('daerah', this.dataAnggota.daerah);      }      if(this.dataAnggota.cabang != undefined && this.dataAnggota.cabang != '') {        this.getOrgDetail('cabang', this.dataAnggota.cabang);      }      if(this.dataAnggota.ranting != undefined && this.dataAnggota.ranting != '') {        this.getOrgDetail('ranting', this.dataAnggota.ranting);      }    });    this.loading = false;  }  getOrgDetail(org, id)  {    this.api.get('org/findone/'+org+'/'+id).subscribe(res=>{      this.orgDetail[id] = res;    })  }  transform(str) {    var splitStr = str.toLowerCase().split(' ');    for (var i = 0; i < splitStr.length; i++) {        splitStr[i] = splitStr[i].charAt(0).toUpperCase() + splitStr[i].substring(1);    }    return splitStr.join(' ');  }  loadingValidasi: boolean;  validasi()  {    this.loadingValidasi = true;    this.api.get('anggota/validate/'+this.dataAnggota.id).subscribe(res=>{      this.loadingValidasi = false;      this.dataAnggota.disetujui = 1;    },er=>{      alert('Tidak dapat melakukan validasi.');      this.loadingValidasi = false;    })  }  loadingPrint: boolean;  printCard()  {    this.loadingPrint = true;    this.api.get('anggota/printcard/'+this.dataAnggota.id).subscribe(res=>{      this.loadingPrint = false;      this.dataAnggota.printed_at = res;      //this.domToImage();      this.htmlToImage();    },er=>{      alert('Tidak dapat memperbarui tanggal cetak kartu.');      this.loadingPrint = false;    })  }  htmlToImage()  {    // var printContents = document.getElementById('print-section').innerHTML;    // var originalContents = document.body.innerHTML;    // document.body.innerHTML = printContents;    // window.print();    // document.body.innerHTML = originalContents;    var node = document.getElementById('print-section');    htmlToImage.toJpeg(node, { quality: 1, height: node.clientHeight, width: node.clientWidth })    .then(function (dataUrl) {      var myWindow = window.open("", "Image");      myWindow.document.write("<img src='"+dataUrl+"''><script></script>");      setTimeout(()=>{        myWindow.print();      },200);    })    .catch(function (error) {      console.error('oops, something went wrong!', error);    });  }  openMedia()  {    let dialogRef = this.dialog.open(ImageUploaderComponent,{      width: '500px',      data: {ratio: 1, keepRatio: true}    });    dialogRef.afterClosed().subscribe(result => {      if(result)      {        this.dataAnggota.image = result;        this.uploadPhoto(result);      }    });  }  uploadPhoto(imageData)  {    this.api.put('anggota/uploadfoto/'+this.dataAnggota.id,{foto: imageData}).subscribe(res=>{      console.log(res);    })  }  selectedFile:File;  fileChangeEvent(fileInput: any) {    if (fileInput.target.files && fileInput.target.files[0]) {      this.myfilename = '';      const file = fileInput.target.files[0];      this.selectedFile = file;      this.myfilename += file.name;      const reader = new FileReader();      reader.onload = (e: any) => {        const image = new Image();        image.src = e.target.result;        image.onload = rs => {          // Return Base64 Data URL          const imgBase64Path = e.target.result;        };      };      reader.readAsDataURL(fileInput.target.files[0]);      // Reset File Input to Selct Same file again      this.uploadFileInput.nativeElement.value = "";    } else {      this.myfilename = 'Select File';    }  }  konfirmasiPayment() {    var conf = confirm('Simpan konfirmasi pembayaran?');    if (conf) {      if(this.paymentData.amount != undefined) {        this.unggahDoc();      } else {        alert('Tambahkan jumlah bayar!');      }    }  }  paymentData:any = {};  unggahDoc(){    this.loading = true;    const formData = new FormData();    formData.append('file', this.selectedFile);    formData.append('name', this.myfilename);    formData.append('amount', this.paymentData.amount);    formData.append('id_user', this.dataAnggota.id);    this.api.uploadDoc('payments/uploadDoc', formData)      .subscribe(res => {        this.loading = false;        this.getDetailAnggota();      }, error => {        this.loading = false;      })  }}
```

#### Template Daftar Anggota

##### daftar-anggota.component.html

```html
<div>  <div class="uk-margin-top">    <form class="uk-search uk-search-default uk-width-1-1">      <span uk-filter-icon></span>      <input        class="uk-search-input"        (keyup)="applySearchResultFilter($event)"        placeholder="Filter data.."        #input      />    </form>  </div>  <table mat-table [dataSource]="dataAnggota" style="width: 100%">    <!-- Position Column -->    <ng-container matColumnDef="position">      <th mat-header-cell *matHeaderCellDef>No.</th>      <td mat-cell *matCellDef="let element">{{ element.position }}</td>    </ng-container>    <!-- Name Column -->    <ng-container matColumnDef="nba">      <th mat-header-cell *matHeaderCellDef>NBA</th>      <td mat-cell *matCellDef="let element">{{ element.nba }}</td>    </ng-container>    <!-- Weight Column -->    <ng-container matColumnDef="nama">      <th mat-header-cell *matHeaderCellDef>Nama</th>      <td mat-cell *matCellDef="let element">{{ element.nama }}</td>    </ng-container>    <!-- Symbol Column -->    <ng-container matColumnDef="wilayah">      <th mat-header-cell *matHeaderCellDef>Wilayah</th>      <td mat-cell *matCellDef="let element">        {{ element.nama_wilayah }} <br>        {{ element.nama_daerah }} <br>        {{ element.nama_cabang }} <br>        {{ element.nama_ranting }}      </td>    </ng-container>    <ng-container matColumnDef="created_at">      <th mat-header-cell *matHeaderCellDef>Tanggal Pengajuan</th>      <td mat-cell *matCellDef="let element">{{ element.created_at }}</td>    </ng-container>       <ng-container matColumnDef="disetujui">      <th mat-header-cell *matHeaderCellDef>Status Keanggotaan</th>      <td mat-cell *matCellDef="let element">        <mat-chip-list>          <mat-chip color="accent">{{ element.disetujui == 1 ? "Valid" : "Menunggu" }}</mat-chip>        </mat-chip-list>              </td>    </ng-container>    <ng-container matColumnDef="#">      <th mat-header-cell *matHeaderCellDef>#</th>      <td mat-cell *matCellDef="let element">        <button mat-button (click)="detailAnggota(element)">Detail</button>      </td>    </ng-container>    <tr mat-header-row *matHeaderRowDef="tableColumns; sticky:true;"></tr>    <tr mat-row *matRowDef="let row; columns: tableColumns"></tr>    <!-- Row shown when there is no matching data. -->    <tr class="mat-row" *matNoDataRow >      <td class="mat-cell" colspan="4">        Tidak ada data yang ditampilkan...      </td>    </tr>      </table> </div>
```

##### daftar-anggota.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DaftarAnggotaComponent } from './daftar-anggota.component';describe('DaftarAnggotaComponent', () => {  let component: DaftarAnggotaComponent;  let fixture: ComponentFixture<DaftarAnggotaComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DaftarAnggotaComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DaftarAnggotaComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### daftar-anggota.component.ts

```typescript
import { Component, Input, OnInit } from '@angular/core';import { MatDialog } from '@angular/material/dialog';import { DialogDetailAnggotaComponent } from '../dialog-detail-anggota/dialog-detail-anggota.component';@Component({  selector: 'tabel-anggota',  templateUrl: './daftar-anggota.component.html',  styleUrls: ['./daftar-anggota.component.scss']})export class DaftarAnggotaComponent implements OnInit {  @Input() dataAnggota: any = [];   @Input() loadingStatus: boolean;   constructor(    private dialog: MatDialog  )   {      }  ngOnInit(): void {  }  tableColumns: any = ['nba', 'nama', 'wilayah', 'created_at', 'disetujui', '#'];  applySearchResultFilter(event: Event) {    console.log('test');    const filterValue = (event.target as HTMLInputElement).value;    this.dataAnggota.filter = filterValue.trim().toLowerCase();  }  detailAnggota(data)  {    const dialogRef = this.dialog.open(DialogDetailAnggotaComponent, {      width: '90%',      height: '100%',      maxHeight: '100%',      data: {dataAnggota: data}    });    dialogRef.afterClosed().subscribe(result => {          });  }}
```

#### Template Form Anggota

##### template-form-anggota.component.html

```html
<h2>Keanggotaan Muhammadiyah</h2>

<div *ngIf="userData != undefined">
<mat-form-field class="uk-width-1-1" appearance="outline">
  <mat-label>NBM</mat-label>
  <input matInput [(ngModel)]="dataAnggota.nbm" [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"/>
</mat-form-field>

<h2>Data Pribadi</h2>
  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Nama</mat-label>
    <input matInput required [(ngModel)]="dataAnggota.nama" [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"  #nama="ngModel" />
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Email</mat-label>
    <input
      matInput
      [(ngModel)]="dataAnggota.email"
      type="email"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
    />
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Nomor HP</mat-label>
    <input
      matInput
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      [(ngModel)]="dataAnggota.no_telp"
    />
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Tempat Lahir</mat-label>
    <input
      type="text"
      required
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      [(ngModel)]="dataAnggota.tempat_lahir"
      matInput
      #tempat_lahir="ngModel"
    />
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Tanggal Lahir</mat-label>
    <input
      matInput
      required
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      [(ngModel)]="dataAnggota.tanggal_lahir"
      #tanggal_lahir="ngModel"
      [matDatepicker]="birthDay"
    />
    <mat-datepicker-toggle matSuffix [for]="birthDay"></mat-datepicker-toggle>
    <mat-datepicker #birthDay></mat-datepicker>
  </mat-form-field>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>RT</mat-label>
      <input
        type="number"
        required
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        [(ngModel)]="dataAnggota.rt"
        #rt="ngModel"
        matInput
      />
    </mat-form-field>

    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>RW</mat-label>
      <input
        type="number"
        required
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        [(ngModel)]="dataAnggota.rw"
        #rw="ngModel"
        matInput
      />
    </mat-form-field>
  </div>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>Provinsi</mat-label>
      <mat-select
        required
        [(ngModel)]="dataAnggota.provinsi"
        #provinsi="ngModel"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataProvinsi.length == 0 && userData.role_id != 'superadmin'"
        (ngModelChange)="getDataKabupaten(dataAnggota.provinsi, 'id_prov')"
      >
        <mat-option
          *ngIf="dataProvinsi.length == 0"
          placeholder="Data belum tersedia."
          >Data belum tersedia.</mat-option
        >
        <mat-option *ngFor="let n of dataProvinsi" value="{{ n.id_prov }}">{{
          n.nama
        }}</mat-option>
      </mat-select>
    </mat-form-field>

    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>Kabupaten/Kota</mat-label>
      <mat-select
        required
        [(ngModel)]="dataAnggota.kabupaten"
        #kabupaten="ngModel"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataAnggota.provinsi == undefined && userData.role_id != 'superadmin'"
        (ngModelChange)="getDataKecamatan(dataAnggota.kabupaten, 'id_kab')"
      >
        <mat-option
          *ngIf="dataKabupaten.length == 0"
          placeholder="Data belum tersedia."
          >Data belum tersedia.</mat-option
        >
        <mat-option *ngFor="let n of dataKabupaten" value="{{ n.id_kab }}">{{
          n.nama
        }}</mat-option>
      </mat-select>
    </mat-form-field>
  </div>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>Kecamatan</mat-label>
      <mat-select
        required
        [(ngModel)]="dataAnggota.kecamatan"
        #kecamatan="ngModel"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataAnggota.kabupaten == undefined && userData.role_id != 'superadmin'"
        (ngModelChange)="getDataKelurahan(dataAnggota.kecamatan, 'id_kec')"
      >
        <mat-option
          *ngIf="dataKecamatan.length == 0"
          placeholder="Data belum tersedia."
          >Data belum tersedia.</mat-option
        >
        <mat-option *ngFor="let n of dataKecamatan" value="{{ n.id_kec }}">{{
          n.nama
        }}</mat-option>
      </mat-select>
    </mat-form-field>

    <mat-form-field class="uk-width-1-2" appearance="outline">
      <mat-label>Desa/Kelurahan</mat-label>
      <mat-select
        required
        [(ngModel)]="dataAnggota.kelurahan"
        #desa="ngModel"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataAnggota.kecamatan == undefined && userData.role_id != 'superadmin'"
      >
        <mat-option
          *ngIf="dataKelurahan.length == 0"
          placeholder="Data belum tersedia."
          >Data belum tersedia.</mat-option
        >
        <mat-option *ngFor="let n of dataKelurahan" value="{{ n.id_kel }}">{{
          n.nama
        }}</mat-option>
      </mat-select>
    </mat-form-field>
  </div>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Alamat</mat-label>
    <textarea
      matInput
      required
      [(ngModel)]="dataAnggota.alamat"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      #alamat="ngModel"
    ></textarea>
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Status</mat-label>
    <mat-select required [(ngModel)]="dataAnggota.status"
     #status="ngModel">
      <mat-option
        *ngFor="let n of [{label:'Menikah',id:'menikah'}, {label:'Belum Menikah',id:'belum_menikah'},{label: 'Duda/Janda',id:'duda_janda'}]; let idx=index"
        value="{{ n.id }}"
        >{{ n.label }}</mat-option
      >
    </mat-select>
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Pekerjaan</mat-label>
    <input
      matInput
      [(ngModel)]="dataAnggota.pekerjaan"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      #pekerjaan="ngModel"
    />
  </mat-form-field>

  <p>Upload Dokumen (pdf)</p>
  
  <div style="text-align: left" *ngIf="dataAnggota.doc_path != null" class="uk-width-1-3">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/PDF_file_icon.svg/267px-PDF_file_icon.svg.png?20181008112808" style="width: 100px"/>
    <div style="padding-top: 10px">
      <button mat-button color="warn" (click)="downloadDoc(dataAnggota.doc_path)">{{myfilename}}</button>
    </div>
  </div>

  <mat-form-field  class="uk-width-1-1">

    <div>

      <mat-toolbar style="font-size: 14px;">

        <!-- Readonly Input to show File names -->
        <input matInput [(ngModel)]="myfilename" readonly name="memberContactNo" />

        <!-- Browse Button -->
        <button mat-flat-button color="primary">
          Browse
        </button>

      </mat-toolbar>

      <!-- Fetch selected filed on change -->
      <input type="file" #UploadFileInput id="fileUpload" (change)="fileChangeEvent($event)" name="fileUpload"
        multiple="multiple" accept="application/pdf" />

    </div>

  </mat-form-field>

  <h2>Pendidikan</h2>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-3-4" appearance="outline">
      <mat-label>SD/MI</mat-label>
      <input [(ngModel)]="dataAnggota.pend_sd"
       #pend_sd="ngModel" matInput />
    </mat-form-field>

    <mat-form-field class="uk-width-1-4" appearance="outline">
      <mat-label>Tahun Lulus</mat-label>
      <input
        type="number"
        [(ngModel)]="dataAnggota.lulus_sd"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        #lulus_sd="ngModel"
        matInput
      />
    </mat-form-field>
  </div>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-3-4" appearance="outline">
      <mat-label>SMP/MTs</mat-label>
      <input
        [(ngModel)]="dataAnggota.pend_smp"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        #pend_smp="ngModel"
        matInput
      />
    </mat-form-field>

    <mat-form-field class="uk-width-1-4" appearance="outline">
      <mat-label>Tahun Lulus</mat-label>
      <input
        type="number"
        [(ngModel)]="dataAnggota.lulus_smp"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        #lulus_smp="ngModel"
        matInput
      />
    </mat-form-field>
  </div>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-3-4" appearance="outline">
      <mat-label>SMA/MA</mat-label>
      <input
        [(ngModel)]="dataAnggota.pend_sma"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        #pend_sma="ngModel"
        matInput
      />
    </mat-form-field>

    <mat-form-field class="uk-width-1-4" appearance="outline">
      <mat-label>Tahun Lulus</mat-label>
      <input
        type="number"
        [(ngModel)]="dataAnggota.lulus_sma"
        [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
        #lulus_sma="ngModel"
        matInput
      />
    </mat-form-field>
  </div>

  <div
    class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"
    uk-grid
  >
    <mat-form-field class="uk-width-3-4" appearance="outline">
      <mat-label>S1</mat-label>
      <input [(ngModel)]="dataAnggota.pend_s1"
       #pend_s1="ngModel" matInput />
    </mat-form-field>

    <mat-form-field class="uk-width-1-4" appearance="outline">
      <mat-label>Tahun Lulus</mat-label>
      <input
        type="number"
        [(ngModel)]="dataAnggota.lulus_s1"

        #lulus_s1="ngModel"
        matInput
      />
    </mat-form-field>
  </div>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Gelar Kesarjanaan yang lain</mat-label>
    <input [(ngModel)]="dataAnggota.gelar"
     #gelar="ngModel" matInput />
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Pendidikan Non Formal</mat-label>
    <input
      [(ngModel)]="dataAnggota.gelar_non"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'"
      #gelar_non="ngModel"
      matInput
    />
  </mat-form-field>

  <h2>Keanggotaan Aisyiyah</h2>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Wilayah</mat-label>
    <mat-select
      required
      [(ngModel)]="dataAnggota.wilayah"
      #wilayah="ngModel"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataWilayah.length == 0 && userData.role_id != 'superadmin'"
      (ngModelChange)="getDataKabupaten(dataAnggota.wilayah, 'id_wilayah')"
    >
      <mat-option
        *ngIf="dataWilayah.length == 0"
        placeholder="Data belum tersedia."
        >Data belum tersedia.</mat-option
      >
      <mat-option *ngFor="let n of dataWilayah" value="{{ n.id_wilayah }}">{{
        n.nama
      }}</mat-option>
    </mat-select>
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Daerah</mat-label>
    <mat-select
      required
      [(ngModel)]="dataAnggota.daerah"
      #daerah="ngModel"
      [disabled]=" dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' &&
        dataDaerah.length == 0 && dataAnggota.wilayah == undefined && userData.role_id != 'superadmin'
      "
      (ngModelChange)="getDataKecamatan(dataAnggota.daerah, 'id_daerah')"
    >
      <mat-option
        *ngIf="dataDaerah.length == 0"
        placeholder="Data belum tersedia."
        >Data belum tersedia.</mat-option
      >
      <mat-option *ngFor="let n of dataDaerah" value="{{ n.id_daerah }}">{{
        n.nama
      }}</mat-option>
    </mat-select>
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Cabang</mat-label>
    <mat-select
      required
      [(ngModel)]="dataAnggota.cabang"
      #cabang="ngModel"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' &&
        dataCabang.length == 0 && dataAnggota.cabang == undefined && userData.role_id != 'superadmin'
      "
      (ngModelChange)="getDataKelurahan(dataAnggota.cabang, 'id_cabang')"
    >
      <mat-option
        *ngIf="dataCabang.length == 0"
        placeholder="Data belum tersedia."
        >Data belum tersedia.</mat-option
      >
      <mat-option *ngFor="let n of dataCabang" value="{{ n.id_cabang }}">{{
        n.nama
      }}</mat-option>
    </mat-select>
  </mat-form-field>

  <mat-form-field class="uk-width-1-1" appearance="outline">
    <mat-label>Ranting</mat-label>
    <mat-select
      required
      [(ngModel)]="dataAnggota.ranting"
      #ranting="ngModel"
      [disabled]="dataAnggota.disetujui == 1 && userData.role_id != 'superadmin' && dataRanting.length == 0 && userData.role_id != 'superadmin'"
    >
      <mat-option
        *ngIf="dataRanting.length == 0"
        placeholder="Data belum tersedia."
        >Data belum tersedia.</mat-option
      >
      <mat-option
        *ngFor="let n of dataRanting"
        value="{{ n.id_ranting }}"
        >{{ n.nama }}</mat-option
      >
    </mat-select>
  </mat-form-field>

  <div class="uk-grid-small" uk-grid>
    <div class="uk-width-expand"></div>
    <div>
      <button
        mat-flat-button
        color="primary"
        [disabled]="
          loading ||
          ranting.invalid ||
          cabang.invalid ||
          daerah.invalid ||
          wilayah.invalid ||
          gelar_non.invalid ||
          gelar.invalid ||
          lulus_s1.invalid ||
          pend_s1.invalid ||
          lulus_sma.invalid ||
          pend_sma.invalid ||
          lulus_smp.invalid ||
          pend_smp.invalid ||
          daerah.invalid ||
          lulus_sd.invalid ||
          pend_sd.invalid ||
          pekerjaan.invalid ||
          status.invalid ||
          alamat.invalid ||
          desa.invalid ||
          kecamatan.invalid ||
          kabupaten.invalid ||
          provinsi.invalid ||
          rt.invalid ||
          rw.invalid ||
          tanggal_lahir.invalid ||
          tempat_lahir.invalid ||
          nama.invalid && userData.role_id != 'superadmin' ||
          dataAnggota.disetujui == 1 && userData.role_id != 'superadmin'
        "
        (click)="simpan()"
      >
        {{ loading ? "Mohon tunggu..." : buttonText }}
      </button>
    </div>
  </div>
  <div style="height: 50px;"></div>
</div>
```

#####  template-form-anggota.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { TemplateFormAnggotaComponent } from './template-form-anggota.component';describe('TemplateFormAnggotaComponent', () => {  let component: TemplateFormAnggotaComponent;  let fixture: ComponentFixture<TemplateFormAnggotaComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ TemplateFormAnggotaComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(TemplateFormAnggotaComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### template-form-anggota.component.ts

```typescript
import { Component, ElementRef, Input, OnInit, ViewChild } from '@angular/core';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'form-anggota',
  templateUrl: './template-form-anggota.component.html',
  styleUrls: ['./template-form-anggota.component.scss']
})
export class TemplateFormAnggotaComponent implements OnInit {

  @ViewChild('UploadFileInput') uploadFileInput: ElementRef;
  myfilename = 'Pilih File';


  @Input() dataAnggota: any = {};
  @Input() buttonText: any;
  dataProvinsi: any = [];
  dataKabupaten: any = [];
  dataKecamatan: any = [];
  dataKelurahan: any = [];

  dataWilayah: any = [];
  dataDaerah: any = [];
  dataCabang: any = [];
  dataRanting: any = [];
  userData:any;
  constructor(
    private api: ApiService
  ) {
  }

  ngOnChanges() {
    this.checkDataAnggota();
  }

  ngOnInit(): void {
    this.getDataProvinsi();
    this.api.me().subscribe(res=>{
      this.userData=res;
    });
  }

  selectedFile:File;
  fileChangeEvent(fileInput: any) {

    if (fileInput.target.files && fileInput.target.files[0]) {

      this.myfilename = '';
      const file = fileInput.target.files[0];
      this.selectedFile = file;
      this.myfilename += file.name;
      this.unggahDoc();

      const reader = new FileReader();
      reader.onload = (e: any) => {
        const image = new Image();
        image.src = e.target.result;
        image.onload = rs => {

          // Return Base64 Data URL
          const imgBase64Path = e.target.result;

        };
      };
      reader.readAsDataURL(fileInput.target.files[0]);

      // Reset File Input to Selct Same file again
      this.uploadFileInput.nativeElement.value = "";
    } else {
      this.myfilename = 'Select File';
    }
  }

  unggahDoc(){
    const formData = new FormData();
    formData.append('file', this.selectedFile);
    formData.append('name', this.myfilename);

    this.api.uploadDoc('anggota/uploadDoc', formData)
      .subscribe(res => {
        this.dataAnggota.doc_path = res;
      })
  }

  checkDataAnggota()
  {
    if(this.dataAnggota.id != undefined)
    {
      if(this.dataAnggota.doc_path != null) {
        this.myfilename = this.dataAnggota.doc_path.replace('files/', '');
      }
      //load data alamat
      if(this.dataAnggota.provinsi != undefined && this.dataAnggota.provinsi != '')
      this.getDataKabupaten(this.dataAnggota.provinsi, 'id_prov');
      if(this.dataAnggota.kabupaten != undefined && this.dataAnggota.kabupaten != '')
      this.getDataKecamatan(this.dataAnggota.kabupaten, 'id_kab');
      if(this.dataAnggota.kecamatan != undefined && this.dataAnggota.kecamatan != '')
      this.getDataKelurahan(this.dataAnggota.kecamatan, 'id_kec');
      //load data keanggotaan
      if(this.dataAnggota.wilayah != undefined && this.dataAnggota.wilayah != '')
      this.getDataKabupaten(this.dataAnggota.wilayah, 'id_wilayah');
      if(this.dataAnggota.daerah != undefined && this.dataAnggota.daerah != '')
      this.getDataKecamatan(this.dataAnggota.daerah, 'id_daerah');
      if(this.dataAnggota.cabang != undefined && this.dataAnggota.cabang != '')
      this.getDataKelurahan(this.dataAnggota.cabang, 'id_cabang');
    }
  }

  getDataProvinsi() {
    this.api.get('org/wilayah').subscribe((res) => {
      this.dataProvinsi = res;
      this.dataWilayah = res;
    });
  }
  getDataKabupaten(id, attr) {
    this.api.get('org/daerah?' + attr + '=' + id).subscribe((res) => {
      if(attr == 'id_prov')
      this.dataKabupaten = res;
      if(attr == 'id_wilayah')
      this.dataDaerah = res;
    });
  }
  getDataKecamatan(id, attr) {
    this.api.get('org/cabang?' + attr + '=' + id).subscribe((res) => {
      if(attr == 'id_kab')
      this.dataKecamatan = res;
      if(attr == 'id_daerah')
      this.dataCabang = res;
    });
  }
  getDataKelurahan(id, attr) {
    this.api.get('org/ranting?' + attr + '=' + id).subscribe((res) => {
      if(attr == 'id_kec')
      this.dataKelurahan = res;
      if(attr == 'id_cabang')
      this.dataRanting = res;
    });
  }

  loading: boolean;
  simpan() {
    this.loading = true;
    if(this.dataAnggota.id == undefined)
    {
      this.saveData();
    }else{
      this.updateData();
    }

  }

  saveData()
  {
    this.api.post('anggota/add', this.dataAnggota).subscribe(
      (res) => {
        this.loading = false;
        alert('Data anggota berhasil ditambahkan. Untuk melihat data, pergi ke halaman daftar anggota.');
        this.dataAnggota = {};
      },
      (err) => {
        this.loading = false;
        alert('Gagal menambahkan data.');
      }
    );
  }

  updateData()
  {
    this.api.put('anggota/update/'+this.dataAnggota.id, this.dataAnggota).subscribe(
      (res) => {
        this.loading = false;
        alert('Data anggota berhasil diperbarui.');
      },
      (err) => {
        this.loading = false;
        alert('Gagal menambahkan data.');
      }
    );
  }

  downloadDoc(url) {
    window.open(this.api.fileBaseUrl+url, 'blank')
  }

}
```

### Dashbord

#### dashbord.component.html

```html
<div style="padding: 20px;">  <mat-card class="uk-margin">    <mat-card-header>      <mat-card-title>Statistik Pimpinan 'Aisyiyah</mat-card-title>    </mat-card-header>    <div class="uk-flex uk-flex-around uk-margin">    <div style="text-align: center;" routerLink="/public/data-wilayah">      <h1 style="font-size: 48px;"> {{dataStatistikOrg.wilayah | number}} </h1>      <h4>Pimpinan Wilayah</h4>    </div>    <div style="text-align: center;" routerLink="/public/data-daerah">      <h1 style="font-size: 48px;"> {{dataStatistikOrg.daerah | number}} </h1>      <h4>Pimpinan Daerah</h4>    </div>    <div style="text-align: center;" routerLink="/public/data-cabang">      <h1 style="font-size: 48px;"> {{dataStatistikOrg.cabang | number}} </h1>      <h4>Pimpinan Cabang</h4>    </div>    <div style="text-align: center;" routerLink="/public/data-ranting">      <h1 style="font-size: 48px;"> {{dataStatistikOrg.ranting | number}} </h1>      <h4>Pimpinan Ranting</h4>    </div>    <div style="text-align: center;">      <h1 style="font-size: 48px;"> {{dataStatistikOrg.istimewa | number}} </h1>      <h4>Pimpinan Cabang Istimewa</h4>    </div>    </div>  </mat-card>  <mat-card class="uk-margin">    <mat-card-header>      <mat-card-title>Jumlah Anggota 'Aisyiyah</mat-card-title>    </mat-card-header>    <div class="uk-flex uk-flex-around uk-margin">    <div style="text-align: center;" routerLink="/public/anggota">      <h1 style="font-size: 48px;"> {{dataStatistikAnggota.num_of_approved_member | number}} </h1>      <h4>Anggota Terverifikasi</h4>    </div>    <div style="text-align: center;" routerLink="/public/anggota">      <h1 style="font-size: 48px;"> {{dataStatistikAnggota.num_of_pending_member | number}} </h1>      <h4>Pengajuan Belum Validasi</h4>    </div>    </div>  </mat-card>  <mat-card class="uk-margin">    <div class="card-header">      <mat-card-title>Anggota 'Aisyiyah</mat-card-title>      <button mat-icon-button mat-button [matMenuTriggerFor]="menuKategori">        <mat-icon>filter_list</mat-icon>      </button>      <mat-menu #menuKategori="matMenu">        <button (click)="menu = 'statistikAnggotaByWilayah'" mat-menu-item >          Berdasarkan Wilayah        </button>        <button (click)="menu = 'statistikAnggotaByDaerah'" mat-menu-item>          Berdasarkan Daerah        </button>      </mat-menu>    </div>    <!-- Table Wilayah -->    <table class="uk-table uk-divider" *ngIf="menu == 'statistikAnggotaByWilayah'">      <thead>        <th>NO</th>        <th>Wilayah</th>        <th>Jumlah Anggota</th>      </thead>      <tbody>        <tr *ngFor="let n of statistikAnggotaByWilayah; let idx=index">          <td>{{idx+1}}</td>          <td>{{n.nama}}</td>          <td>{{n.num_of_member | number}}</td>        </tr>      </tbody>    </table>    <!-- Table Daerah -->    <table class="uk-table uk-divider" *ngIf="menu == 'statistikAnggotaByDaerah'">      <thead>        <th>NO</th>        <th>Daerah</th>        <th>Jumlah Anggota</th>      </thead>      <tbody>        <tr *ngFor="let n of statistikAnggotaByDaerah; let idx=index">          <td>{{idx+1}}</td>          <td>{{n.nama}}</td>          <td>{{n.num_of_member | number}}</td>        </tr>      </tbody>    </table>  </mat-card></div>
```

#### dashbord.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DashbordComponent } from './dashbord.component';describe('DashbordComponent', () => {  let component: DashbordComponent;  let fixture: ComponentFixture<DashbordComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DashbordComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DashbordComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

#### dashbord.component.ts

```typescript
import { Component, OnInit } from '@angular/core';import { ApiService } from 'src/app/services/api.service';@Component({  selector: 'app-dashbord',  templateUrl: './dashbord.component.html',  styleUrls: ['./dashbord.component.scss']})export class DashbordComponent implements OnInit {  constructor(    public api:ApiService  ) { }  async ngOnInit(): Promise<void> {    await this.getStatistikAnggotaByDaerah();    await this.getStatistikAnggota();    await this.getStatistikOrg();    await this.getStatistikAnggotaByWilayah();  }  dataStatistikAnggota: any = {};  getStatistikAnggota()  {    this.api.get('dashboard/anggota').subscribe(res=>{      this.dataStatistikAnggota = res;    });  }  dataStatistikOrg: any = {};  getStatistikOrg()  {    this.api.get('dashboard/org').subscribe(res=>{      this.dataStatistikOrg = res;    });  }  menu = 'statistikAnggotaByWilayah';  statistikAnggotaByWilayah: any = [];  getStatistikAnggotaByWilayah()  {    this.api.get('dashboard/anggotawilayah').subscribe(res=>{      this.statistikAnggotaByWilayah = res;    });  }  statistikAnggotaByDaerah: any = [];  getStatistikAnggotaByDaerah()  {    this.api.get('dashboard/anggotadaerah').subscribe(res=>{      this.statistikAnggotaByDaerah = res;    });  }}
```

### Database

#### Data Cabang

##### data-cabang.component.html

```html
<h1 style="padding: 30px; margin: 0px;">Data Cabang</h1>

<mat-card>
    <div class="uk-grid-small" uk-grid>

      <mat-form-field class="uk-width-1-4" appearance="outline">
        <mat-label>Cari Cabang ...</mat-label>
        <input matInput (keydown)="applyFilter($event.target.value)" type="search">
      </mat-form-field>
        <div class="uk-width-expand"></div>
        <div>
            <button mat-flat-button color="primary" (click)="dialogCabang(null)">
              Tambah Cabang
            </button>
        </div>

    </div>
    <mat-card-content>
      <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">

        <ng-container matColumnDef="id_cabang">
          <th mat-sort-header="id_cabang" mat-header-cell *matHeaderCellDef> Kode Cabang </th>
          <td mat-cell *matCellDef="let element"> {{element.id_cabang}} </td>
        </ng-container>

        <ng-container matColumnDef="nama_cabang">
          <th mat-sort-header="nama_cabang" mat-header-cell *matHeaderCellDef> Nama Cabang </th>
          <td mat-cell *matCellDef="let element"> {{element.nama}} </td>
        </ng-container>

      

        <ng-container matColumnDef="daerah">
          <th mat-sort-header="daerah" mat-header-cell *matHeaderCellDef> Daerah </th>
          <td mat-cell *matCellDef="let element"> {{element.nama_daerah}} </td>
        </ng-container>

        <ng-container matColumnDef="wilayah">
          <th mat-sort-header="wilayah" mat-header-cell *matHeaderCellDef> Wilayah </th>
          <td mat-cell *matCellDef="let element"> {{element.nama_wilayah}} </td>
        </ng-container>

        <!-- <ng-container matColumnDef="cabang">
          <th mat-sort-header="cabang" mat-header-cell *matHeaderCellDef> Alamat Kantor </th>
          <td mat-cell *matCellDef="let element"> {{dataKecamatan[element.daerah] == undefined ? 'Kosong':dataKecamatan[element.daerah].nama_kecamatan}} </td>
        </ng-container> -->

        <ng-container matColumnDef="verified">
          <th mat-sort-header="verified" mat-header-cell *matHeaderCellDef> Verifikasi Keberadaan </th>
          <td mat-cell *matCellDef="let element"> 
            <mat-icon *ngIf="element.verified == 1" color="primary">check</mat-icon>  
          </td>
        </ng-container>

        <ng-container matColumnDef="no_sk">
          <th mat-sort-header="no_sk" mat-header-cell *matHeaderCellDef> No SK </th>
          <td mat-cell *matCellDef="let element"> {{element.no_sk}} </td>
        </ng-container>

        <ng-container matColumnDef="#">
            <th mat-header-cell *matHeaderCellDef> # </th>
            <td mat-cell *matCellDef="let element; let i=index">
                <button mat-icon-button [matMenuTriggerFor]="menu">
                    <mat-icon>more_vert</mat-icon>
                </button>
                <mat-menu #menu="matMenu">
                  <button mat-menu-item (click)="dialogCabang(element)"><mat-icon>create</mat-icon>Edit</button>
                  <button mat-menu-item (click)="verification(element)"><mat-icon>check</mat-icon>{{element.verified == '1' ? 'Batalkan Verifikasi' : 'Verifikasi Keberadaan'}}</button>
                  <button mat-menu-item (click)="hapus(element.id_cabang)"><mat-icon>delete</mat-icon>Hapus</button>
                </mat-menu>
            </td>
        </ng-container>
        <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true;"></tr>
        <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>
    </table>
    <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>
    <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>

    </mat-card-content>
  </mat-card>
```

##### data-cabang.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { DataCabangComponent } from './data-cabang.component';

describe('DataCabangComponent', () => {
  let component: DataCabangComponent;
  let fixture: ComponentFixture<DataCabangComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ DataCabangComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(DataCabangComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

##### data-cabang.component.ts

```typescript
import { Component, Inject, OnInit, ViewChild } from '@angular/core';import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { ApiService } from 'src/app/services/api.service';import { Sort } from '@angular/material/sort';import { MatTableDataSource } from '@angular/material/table';import { MatPaginator } from '@angular/material/paginator';import { DialogHapusComponent } from '../../dialog-hapus/dialog-hapus.component';import { Router } from '@angular/router';@Component({  selector: 'app-data-cabang',  templateUrl: './data-cabang.component.html',  styleUrls: ['./data-cabang.component.scss']})export class DataCabangComponent implements OnInit {  sortedData:any = [];  dataSource = new MatTableDataSource(this.sortedData);  displayedColumn: string[] = ['id_cabang', 'nama_cabang', 'daerah', 'wilayah', 'verified', 'no_sk','#'];  @ViewChild(MatPaginator) paginator: MatPaginator;  constructor(    private router:Router,    public dialog: MatDialog,    public api:ApiService    ) { }  showSpinner: boolean = true;  ngOnInit(): void {    this.checkRole();    this.getData();    }  userData:any = {};  checkRole()  {    this.api.get('users/me').subscribe(res=>{      this.userData=res;        },err=>{      this.router.navigate(['auth/login']);    })  }  data:any=[];  headers:any=[];  getData()  {    this.api.get('org/cabang').subscribe(res=>{      this.data=res;      this.sortedData = this.data.slice();      this.dataSource = new MatTableDataSource(this.sortedData);      this.dataSource.paginator = this.paginator;      this.showSpinner = false;    })  }  applyFilter(filterValue: string) {    filterValue = filterValue.trim(); // Remove whitespace    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches    this.dataSource.filter = filterValue;  }    sortData(sort: Sort) {    const data = this.data.slice();    if (!sort.active || sort.direction === '') {      this.sortedData = data;      return;    }    this.sortedData = data.sort((a, b) => {      const isAsc = sort.direction === 'asc';      switch (sort.active) {        case 'id_cabang': return compare(a.id_cabang, b.id_cabang, isAsc);        case 'nama_cabang': return compare(a.nama_cabang, b.nama_cabang, isAsc);        case 'wilayah': return compare(a.wilayah, b.wilayah, isAsc);        case 'daerah': return compare(a.daerah, b.daerah, isAsc);        case 'verified': return compare(a.verified, b.verified, isAsc);        case 'no_sk': return compare(a.no_sk, b.no_sk, isAsc);        default: return 0;      }    });    this.dataSource = new MatTableDataSource(this.sortedData);    this.dataSource.paginator = this.paginator;  }  loading:boolean;  hapus(id) {    const dialogRef = this.dialog.open(DialogHapusComponent, {      width: '450px',      data: {id:id, from: 'cabang'}    });    dialogRef.afterClosed().subscribe(result => {      this.loading = false;      if(result != undefined) {        alert('Data cabang berhasil dihapus.');        this.getData();      }    },(err) => {      this.loading = false;      alert('Gagal menghapus data.');    });  }  verification(data)  {    let putData = data.verified == '1' ? 0 : 1;    this.api.put('org/cabang/'+data.id_cabang,{verified:putData}).subscribe(res=>{      //let idx = this.data.map((e)=>{return e.id_wilayah}).indexOf(data.id_wilayah);      data.verified = putData;    })  }    //DIALOG    dialogCabang(data): void {      const dialogRef = this.dialog.open(DialogCabang, {        width: '450px',        data: {data:data}      });      dialogRef.afterClosed().subscribe(result => {        console.log('The dialog was closed');        this.data = [];              this.getData();      });    }}function compare(a: number | string, b: number | string, isAsc: boolean) {  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);}//DIALOG@Component({  selector: 'dialog-container',  templateUrl: 'dialog-cabang.html',})export class DialogCabang {  dataCabang:any = {};  isCreated:boolean;  constructor(    private router: Router,    public api:ApiService,    public dialogRef: MatDialogRef<DialogCabang>,    @Inject(MAT_DIALOG_DATA) public sourceData: any  ) {    this.checkRole();    this.getWilayah();    this.getDaerah();    if(sourceData.data != null) {      this.isCreated = false;           this.getDetailCabang(sourceData.data.id_cabang);    } else {      this.isCreated = true;    }      }  userData:any = {};  checkRole()  {    this.api.get('users/me').subscribe(res=>{      this.userData=res;          if(this.userData.role_id == "admin_wilayah") {        this.dataCabang.id_wilayah = this.userData.kode_area;        this.getDaerah();      } else if(this.userData.role_id == "admin_daerah") {        this.getDetailDaerah(this.userData.kode_area);      }    },err=>{      this.router.navigate(['auth/login']);    })  }  detailDaerah:any;  getDetailDaerah(id_daerah) {    this.api.get('org/findone/daerah/'+id_daerah).subscribe(res=>{      this.detailDaerah = res;      this.getDetailWilayah(this.detailDaerah.id_prov);    })  }  detailWilayah:any;  getDetailWilayah(id_prov)  {    this.api.get('org/findWilayahByProv/wilayah/'+id_prov).subscribe(res=>{      this.detailWilayah = res;      this.dataCabang.id_wilayah = this.detailWilayah.id_wilayah;      this.getWilayah();      this.dataCabang.id_daerah = this.userData.kode_area;    })  }  getDetailCabang(id_cabang)  {    this.api.get('org/findone/cabang/'+id_cabang).subscribe(res=>{      this.dataCabang = res;      this.getWilayah();      this.getDaerah();    })  }  dataWilayah:any = [];  getWilayah() {    this.api.get('org/wilayah').subscribe(res=>{      this.dataWilayah=res;    })  }  dataDaerah:any = [];  getDaerah() {    this.api.get('org/daerah?' + 'id_wilayah' + '=' + this.dataCabang.id_wilayah).subscribe((res) => {      this.dataDaerah=res;        });  }  loading:boolean;  simpan() {    this.loading = true;    if(this.isCreated == true) {      this.api.post('org/cabang', this.dataCabang).subscribe(res=>{        this.loading = false;        alert('Data cabang berhasil ditambahkan.');        this.dialogRef.close();      },(err) => {        this.loading = false;        alert('Gagal menyimpan data.');      });    } else {      this.api.put('org/cabang/'+this.dataCabang.id_cabang, this.dataCabang).subscribe(res=>{        this.loading = false;        alert('Data cabang berhasil diperbarui.');        this.dialogRef.close();      },(err) => {        this.loading = false;        alert('Gagal memperbarui data.');      });    }  }  onNoClick(): void {    this.dialogRef.close();  }}
```

#####  dialog-cabang.html 

```html
<h1 mat-dialog-title>{{isCreated == false ? 'Edit':'Tambah'}} Cabang</h1><div mat-dialog-content>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Kode Cabang</mat-label>      <input [(ngModel)]="dataCabang.id_cabang" required #id="ngModel" [disabled]="isCreated == false" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline" *ngIf="dataWilayah.length > 0">      <mat-label>Wilayah</mat-label>      <mat-select [(ngModel)]="dataCabang.id_wilayah" [disabled]="dataWilayah.length == 0 || userData.role_id == 'admin_wilayah' || userData.role_id == 'admin_daerah'" (ngModelChange)="getDaerah()">        <mat-option *ngFor="let n of dataWilayah" value="{{n.id_wilayah}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Daerah</mat-label>      <mat-select [(ngModel)]="dataCabang.id_daerah" required #id_daerah="ngModel" [disabled]="dataCabang.id_wilayah == undefined || userData.role_id == 'admin_daerah'">        <mat-option *ngFor="let n of dataDaerah"  value="{{n.id_daerah}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kecamatan</mat-label>      <input [(ngModel)]="dataCabang.nama" required #nama="ngModel" matInput>    </mat-form-field>    <!-- <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kantor Cabang</mat-label>      <input [(ngModel)]="dataCabang.nama_kantor" required #nama="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Alamat Kantor</mat-label>      <input [(ngModel)]="dataCabang.alamat_kantor" required #nama="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nomor Telepon</mat-label>      <input type="number" [(ngModel)]="dataCabang.phone_kantor" required #telepon="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Email</mat-label>      <input matInput [(ngModel)]="dataCabang.email_kantor" required #email="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama SE</mat-label>      <input matInput [(ngModel)]="dataCabang.nama_se">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>HP SE</mat-label>      <input matInput [(ngModel)]="dataCabang.hp_se">    </mat-form-field> -->    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>No SK</mat-label>      <input matInput [(ngModel)]="dataCabang.no_sk">    </mat-form-field></div><div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Batal</button>  <button mat-raised-button color="primary" [disabled]="loading || id.invalid || id_daerah.invalid || nama.invalid" (click)="simpan()">{{loading ? 'Mohon tunggu...':'Simpan'}}</button></div>
```

#### Data Daerah

##### data-daerah.component.html

```html
<h1 style="padding: 30px; margin: 0px;">Data Daerah</h1>  <mat-card>    <div class="uk-grid-small" uk-grid>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Cari Daerah ...</mat-label>        <input matInput (keydown)="applyFilter($event.target.value)" type="search">      </mat-form-field>        <div class="uk-width-expand"></div>        <div>            <button mat-flat-button color="primary" (click)="dialogDaerah(null)">              Tambah Daerah            </button>        </div>    </div>    <mat-card-content>      <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">        <ng-container matColumnDef="id_daerah">          <th mat-sort-header="id_daerah" mat-header-cell *matHeaderCellDef> Kode Daerah </th>          <td mat-cell *matCellDef="let element"> {{element.id_daerah}} </td>        </ng-container>        <ng-container matColumnDef="nama_daerah">          <th mat-sort-header="nama_daerah" mat-header-cell *matHeaderCellDef> Nama Daerah </th>          <td mat-cell *matCellDef="let element"> {{element.nama}} </td>        </ng-container>        <ng-container matColumnDef="id_wilayah">          <th mat-sort-header="id_wilayah" mat-header-cell *matHeaderCellDef> Wilayah </th>          <td mat-cell *matCellDef="let element"> {{element.nama_wilayah}} </td>        </ng-container>        <!-- <ng-container matColumnDef="id_kab">          <th mat-sort-header="id_kab" mat-header-cell *matHeaderCellDef> Alamat Kantor </th>          <td mat-cell *matCellDef="let element"> {{dataKabupaten[element.id_kab] == undefined ? 'Kosong':dataKabupaten[element.id_kab].nama_kabupaten}} </td>        </ng-container> -->        <ng-container matColumnDef="verified">          <th mat-sort-header="verified" mat-header-cell *matHeaderCellDef> Verifikasi Keberadaan </th>          <td mat-cell *matCellDef="let element"> <mat-icon color="primary" *ngIf="element.verified == 1">check</mat-icon> </td>        </ng-container>        <ng-container matColumnDef="no_sk">          <th mat-sort-header="no_sk" mat-header-cell *matHeaderCellDef> No SK </th>          <td mat-cell *matCellDef="let element"> {{element.no_sk}} </td>        </ng-container>        <ng-container matColumnDef="#">            <th mat-header-cell *matHeaderCellDef> # </th>            <td mat-cell *matCellDef="let element; let i=index">                <button mat-icon-button [matMenuTriggerFor]="menu">                    <mat-icon>more_vert</mat-icon>                </button>                <mat-menu #menu="matMenu">                  <button mat-menu-item (click)="dialogDaerah(element)"><mat-icon>create</mat-icon>Edit</button>                  <button mat-menu-item (click)="verification(element)"><mat-icon>check</mat-icon>{{element.verified == '1' ? 'Batalkan Verifikasi' : 'Verifikasi Keberadaan'}}</button>                  <button mat-menu-item (click)="hapus(element.id_daerah)"><mat-icon>delete</mat-icon>Hapus</button>                </mat-menu>            </td>        </ng-container>        <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true"></tr>        <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>    </table>    <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>    <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card-content>  </mat-card>
```

##### data-daerah.component.spec.ts 

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DataDaerahComponent } from './data-daerah.component';describe('DataDaerahComponent', () => {  let component: DataDaerahComponent;  let fixture: ComponentFixture<DataDaerahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DataDaerahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DataDaerahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### data-daerah.component.ts 

```typescript
import { Component, Inject, OnInit, ViewChild } from '@angular/core';
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { ApiService } from 'src/app/services/api.service';
import { Sort } from '@angular/material/sort';
import { MatTableDataSource } from '@angular/material/table';
import { MatPaginator } from '@angular/material/paginator';
import { DialogHapusComponent } from '../../dialog-hapus/dialog-hapus.component';
import { Router } from '@angular/router';

@Component({
  selector: 'app-data-daerah',
  templateUrl: './data-daerah.component.html',
  styleUrls: ['./data-daerah.component.scss']
})
export class DataDaerahComponent implements OnInit {

  sortedData:any = [];
  dataSource = new MatTableDataSource(this.sortedData);
  displayedColumn: string[] = ['id_daerah', 'nama_daerah', 'id_wilayah', 'verified', 'no_sk','#'];
  @ViewChild(MatPaginator) paginator: MatPaginator;
  constructor(
    private router: Router,
    public dialog: MatDialog,
    public api:ApiService
    ) { }

  showSpinner: boolean = true;
  ngOnInit(): void {
    this.checkRole();
  }

  userData:any = {};
  checkRole()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res; 
      this.getData();   
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  data:any=[];
  headers:any=[];
  getData()
  {
    this.api.get('org/daerah').subscribe(res=>{
      this.data=res;
      this.sortedData = this.data.slice();
      this.dataSource = new MatTableDataSource(this.sortedData);
      this.dataSource.paginator = this.paginator;
      this.showSpinner = false;
    })
  }

  applyFilter(filterValue: string) {
    filterValue = filterValue.trim(); // Remove whitespace
    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches
    this.dataSource.filter = filterValue;
  }

  sortData(sort: Sort) {
    const data = this.data.slice();
    if (!sort.active || sort.direction === '') {
      this.sortedData = data;
      return;
    }

    this.sortedData = data.sort((a, b) => {
      const isAsc = sort.direction === 'asc';
      switch (sort.active) {
        case 'id_daerah': return compare(a.id_daerah, b.id_daerah, isAsc);
        case 'nama_daerah': return compare(a.nama_daerah, b.nama_daerah, isAsc);
        case 'id_wilayah': return compare(a.id_wilayah, b.id_wilayah, isAsc);
        case 'verified': return compare(a.verified, b.verified, isAsc);
        case 'no_sk': return compare(a.no_sk, b.no_sk, isAsc);
        default: return 0;
      }
    });
    this.dataSource = new MatTableDataSource(this.sortedData);
    this.dataSource.paginator = this.paginator;
  }

  loading:boolean;
  hapus(id) {
    const dialogRef = this.dialog.open(DialogHapusComponent, {
      width: '450px',
      data: {id:id, from: 'daerah'}
    });
    dialogRef.afterClosed().subscribe(result => {
      this.loading = false;
      if(result != undefined) {
        alert('Data daerah berhasil dihapus.');
        this.getData();
      }
    },(err) => {
      this.loading = false;
      alert('Gagal menghapus data.');
    });
  }

  verification(data)
  {
    let putData = data.verified == '1' ? 0 : 1;
    this.api.put('org/daerah/'+data.id_daerah,{verified:putData}).subscribe(res=>{
      //let idx = this.data.map((e)=>{return e.id_wilayah}).indexOf(data.id_wilayah);
      data.verified = putData;
    })
  }

  //DIALOG
  dialogDaerah(data): void {
    const dialogRef = this.dialog.open(DialogDaerah, {
      width: '450px',
      data: {data:data}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      this.data = [];
      this.getData();
    });
  }

}

function compare(a: number | string, b: number | string, isAsc: boolean) {
  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);
}

//DIALOG
@Component({
  selector: 'dialog-container',
  templateUrl: 'dialog-daerah.html',
})
export class DialogDaerah {

  dataDaerah:any = {};
  isCreated:boolean;
  constructor(
    private router: Router,
    public api:ApiService,
    public dialogRef: MatDialogRef<DialogDaerah>,
    @Inject(MAT_DIALOG_DATA) public sourceData: any
  ) {
    this.checkRole();
    this.getWilayah();
    if(sourceData.data != null) {
      this.isCreated = false;
     this.getDetailDaerah(sourceData.data.id_daerah);     
    } else {
      this.isCreated = true;
    }
   
  }

  userData:any = {};
  checkRole()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res;    
      if(this.userData.role_id == "admin_wilayah") {
        this.dataDaerah.id_wilayah = this.userData.kode_area;
      }
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  getDetailDaerah(id_daerah)
  {
    this.api.get('org/findone/daerah/'+id_daerah).subscribe(res=>{
      this.dataDaerah = res;
    })
  }

  dataWilayah:any = [];
  getWilayah() {
    this.api.get('org/wilayah').subscribe(res=>{
      this.dataWilayah=res;
    })
  }

 

 
  loading:boolean;
  simpan() {
    this.loading = true;
    if(this.isCreated == true) {
      this.api.post('org/daerah', this.dataDaerah).subscribe(res=>{
        this.loading = false;
        alert('Data daerah berhasil ditambahkan.');
        this.dialogRef.close();
      },(err) => {
        this.loading = false;
        alert('Gagal menyimpan data.');
      });
    } else {
      this.api.put('org/daerah/'+this.dataDaerah.id_daerah, this.dataDaerah).subscribe(res=>{
        this.loading = false;
        alert('Data daerah berhasil diperbarui.');
        this.dialogRef.close();
      },(err) => {
        this.loading = false;
        alert('Gagal memperbarui data.');
      });
    }
  }

  onNoClick(): void {
    this.dialogRef.close();
  }

}

```

##### dialog-daerah.html 

```html
<h1 mat-dialog-title>{{isCreated == false ? 'Edit':'Tambah'}} Daerah</h1><div mat-dialog-content>  <mat-form-field class="uk-width-1-1" appearance="outline" *ngIf="dataWilayah.length > 0">    <mat-label>Wilayah</mat-label>    <mat-select [(ngModel)]="dataDaerah.id_wilayah" [disabled]="dataWilayah.length == 0 || userData.role_id == 'admin_wilayah'">      <mat-option *ngFor="let n of dataWilayah" value="{{n.id_wilayah}}">{{n.nama}}</mat-option>    </mat-select>  </mat-form-field>  <!-- <mat-form-field class="uk-width-1-1" appearance="outline" *ngIf="dataDaerah.id_wilayah != undefined">    <mat-label>Alamat Kantor</mat-label>    <mat-select [(ngModel)]="dataDaerah.id_daerah" required #daerah="ngModel">      <mat-option *ngFor="let n of dataKantor" value="{{n.id_kab}}">{{n.nama}}</mat-option>    </mat-select>  </mat-form-field> -->    <mat-form-field class="uk-width-1-1" appearance="outline">        <mat-label>Kode Daerah</mat-label>        <input [disabled]="isCreated == false" [(ngModel)]="dataDaerah.id_daerah" required #id="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kabupaten/Kota</mat-label>      <input [(ngModel)]="dataDaerah.nama" required #nama="ngModel" matInput>    </mat-form-field>    <!-- <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kantor</mat-label>      <input [(ngModel)]="dataDaerah.nama_kantor" required #nama="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nomor Telepon</mat-label>      <input type="number" [(ngModel)]="dataDaerah.phone" required #telepon="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Email</mat-label>      <input matInput [(ngModel)]="dataDaerah.email" required #email="ngModel">    </mat-form-field>   -->    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>No SK</mat-label>      <input matInput [(ngModel)]="dataDaerah.no_sk">    </mat-form-field>    <!-- <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama SE</mat-label>      <input matInput [(ngModel)]="dataDaerah.nama_se">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>HP SE</mat-label>      <input matInput [(ngModel)]="dataDaerah.hp_se">    </mat-form-field> --></div><div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Batal</button>  <button mat-raised-button color="primary" [disabled]="loading || id.invalid || nama.invalid" (click)="simpan()">{{loading ? 'Mohon tunggu...':'Simpan'}}</button></div>
```

#### Data Ranting

##### data-ranting.component.html

```html
<h1 style="padding: 30px; margin: 0px;">Data Ranting</h1>  <mat-card>    <div class="uk-grid-small" uk-grid>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Cari Ranting ...</mat-label>        <input matInput (keydown)="applyFilter($event.target.value)" type="search">      </mat-form-field>        <div class="uk-width-expand"></div>        <div>            <button mat-flat-button color="primary" (click)="dialogRanting(null)">              Tambah Ranting            </button>        </div>    </div>    <mat-card-content>      <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">        <ng-container matColumnDef="id_ranting">          <th mat-sort-header="id_ranting" mat-header-cell *matHeaderCellDef> Kode Ranting </th>          <td mat-cell *matCellDef="let element"> {{element.id_ranting}} </td>        </ng-container>        <ng-container matColumnDef="nama_ranting">          <th mat-sort-header="nama_ranting" mat-header-cell *matHeaderCellDef> Nama Ranting </th>          <td mat-cell *matCellDef="let element"> {{element.nama}} </td>        </ng-container>                   <ng-container matColumnDef="cabang">          <th mat-sort-header="cabang" mat-header-cell *matHeaderCellDef> Cabang </th>          <td mat-cell *matCellDef="let element"> {{ element.nama_cabang }} </td>        </ng-container>        <ng-container matColumnDef="verified">          <th mat-sort-header="verified" mat-header-cell *matHeaderCellDef> Verifikasi Keberadaan </th>          <td mat-cell *matCellDef="let element">             <mat-icon color="primary" *ngIf="element.verified == 1">check</mat-icon>          </td>        </ng-container>        <ng-container matColumnDef="no_sk">          <th mat-sort-header="no_sk" mat-header-cell *matHeaderCellDef> No SK </th>          <td mat-cell *matCellDef="let element"> {{element.no_sk}} </td>        </ng-container>        <ng-container matColumnDef="#">            <th mat-header-cell *matHeaderCellDef> # </th>            <td mat-cell *matCellDef="let element; let i=index">                <button mat-icon-button [matMenuTriggerFor]="menu">                    <mat-icon>more_vert</mat-icon>                </button>                <mat-menu #menu="matMenu">                  <button mat-menu-item (click)="dialogRanting(element)"><mat-icon>create</mat-icon>Edit</button>                  <button mat-menu-item (click)="verification(element)"><mat-icon>check</mat-icon>{{element.verified == '1' ? 'Batalkan Verifikasi' : 'Verifikasi Keberadaan'}}</button>                  <button mat-menu-item (click)="hapus(element.id_ranting)"><mat-icon>delete</mat-icon>Hapus</button>                </mat-menu>            </td>        </ng-container>        <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true"></tr>        <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>    </table>    <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>    <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons (page)="getNext($event)"></mat-paginator>    </mat-card-content>  </mat-card>
```

##### data-ranting.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DataRantingComponent } from './data-ranting.component';describe('DataRantingComponent', () => {  let component: DataRantingComponent;  let fixture: ComponentFixture<DataRantingComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DataRantingComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DataRantingComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### data-ranting.component.ts

```typescript
import { Component, Inject, OnInit, ViewChild } from '@angular/core';
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { ApiService } from 'src/app/services/api.service';
import { Sort } from '@angular/material/sort';
import { MatTableDataSource } from '@angular/material/table';
import { MatPaginator, PageEvent } from '@angular/material/paginator';
import { DialogHapusComponent } from '../../dialog-hapus/dialog-hapus.component';
import { Router } from '@angular/router';


@Component({
  selector: 'app-data-ranting',
  templateUrl: './data-ranting.component.html',
  styleUrls: ['./data-ranting.component.scss']
})
export class DataRantingComponent implements OnInit {

  sortedData:any = [];
  dataSource = new MatTableDataSource(this.sortedData);
  displayedColumn: string[] = ['id_ranting', 'nama_ranting','cabang', 'verified', 'no_sk', '#'];
  @ViewChild(MatPaginator) paginator: MatPaginator;
  constructor(
    private router: Router,
    public dialog: MatDialog,
    public api:ApiService
    ) { }

  showSpinner: boolean = true;
  ngOnInit(): void {
    this.checkRole();
    this.getWilayah();
    this.getDaerah();
    this.getCabang();
    this.getData();
  }

  userData:any = {};
  checkRole()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res;   
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  data:any=[];
  headers:any=[];
  getData()
  {
    this.api.get('org/ranting').subscribe(res=>{
      this.data=res;
      this.sortedData = this.data.slice();
      this.dataSource = new MatTableDataSource(this.sortedData);
      this.dataSource.paginator = this.paginator;
      this.showSpinner = false;
    }, error => {
      console.log(error)
    })
  }

  dataWilayah:any = {};
  getWilayah() {
    this.api.get('org/wilayah').subscribe(res=>{
      this.parseWilayah(res);
    });
  }

  parseWilayah(res) {
    for(var i=0; i<res.length; i++) {
      const idx = res[i].id_wilayah;
      if(this.dataWilayah[idx] == undefined )
      {
        this.dataWilayah[idx] = res[i];
      }
    }
  }

  dataDaerah:any = {};
  getDaerah() {
    this.api.get('org/daerah').subscribe(res=>{
      this.parseDaerah(res);
    });
  }

  parseDaerah(res) {
    for(var i=0; i<res.length; i++) {
      const idx = res[i].id_daerah;
      if(this.dataDaerah[idx] == undefined )
      {
        this.dataDaerah[idx] = res[i];
      }
    }
  }

  dataCabang:any = {};
  getCabang() {
    this.api.get('org/cabang').subscribe(res=>{
      this.parseCabang(res);
    });
  }

  parseCabang(res) {
    for(var i=0; i<res.length; i++) {
      const idx = res[i].id_cabang;
      if(this.dataCabang[idx] == undefined )
      {
        this.dataCabang[idx] = res[i];
      }
    }
  }

  applyFilter(filterValue: string) {
    filterValue = filterValue.trim(); // Remove whitespace
    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches
    this.dataSource.filter = filterValue;
  }
  
  sortData(sort: Sort) {
    const data = this.data.slice();
    if (!sort.active || sort.direction === '') {
      this.sortedData = data;
      return;
    }

    this.sortedData = data.sort((a, b) => {
      const isAsc = sort.direction === 'asc';
      switch (sort.active) {
        case 'id_ranting': return compare(a.id_ranting, b.id_ranting, isAsc);
        case 'nama_ranting': return compare(a.nama_ranting, b.nama_ranting, isAsc);
        case 'verified': return compare(a.verified, b.verified, isAsc);
        case 'no_sk': return compare(a.no_sk, b.no_sk, isAsc);
        default: return 0;
      }
    });
    this.dataSource = new MatTableDataSource(this.sortedData);
    this.dataSource.paginator = this.paginator;
  }

  loading:boolean;
  hapus(id) {
    const dialogRef = this.dialog.open(DialogHapusComponent, {
      width: '450px',
      data: {id:id, from: 'ranting'}
    });
    dialogRef.afterClosed().subscribe(result => {
      this.loading = false;
      if(result != undefined) {
        alert('Data ranting berhasil dihapus.');
        this.getData();
      }
    },(err) => {
      this.loading = false;
      alert('Gagal menghapus data.');
    });
  }

  //DIALOG
  dialogRanting(data): void {
    const dialogRef = this.dialog.open(DialogRanting, {
      width: '450px',
      data: {data:data}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      this.data = [];
      this.dataWilayah = {};
      this.dataDaerah = {};
      this.dataCabang = {};
      this.getWilayah();
      this.getDaerah();
      this.getCabang();
      this.getData();
    });
  }

  verification(data)
  {
    let putData = data.verified == '1' ? 0 : 1;
    this.api.put('org/ranting/'+data.id_ranting,{verified:putData}).subscribe(res=>{     
      data.verified = putData;
    })
  }

  getNext(event: PageEvent) {
    var offset = event.pageSize * event.pageIndex
    this.getData();
  }

}

function compare(a: number | string, b: number | string, isAsc: boolean) {
  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);
}

//DIALOG
@Component({
  selector: 'dialog-container',
  templateUrl: 'dialog-ranting.html',
})
export class DialogRanting {

  dataRanting:any = {};
  isCreated:boolean;
  constructor(
    private router: Router,
    public api:ApiService,
    public dialogRef: MatDialogRef<DialogRanting>,
    @Inject(MAT_DIALOG_DATA) public sourceData: any
  ) {
    if(sourceData.data != null) {
      this.isCreated = false;
      this.dataRanting = sourceData.data;
      this.getWilayah();
      this.getDaerah();
      this.getCabang();
    } else {
      this.checkRole(sourceData);
      this.isCreated = true;
    }
  }

  userData:any = {};
  checkRole(sourceData)
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res;    
      if(sourceData.data == null) {
        if(this.userData.role_id == "admin_wilayah") {
          this.dataRanting.id_wilayah = this.userData.kode_area;
          this.getWilayah();
          this.getDaerah();
          this.getCabang();
        } else if(this.userData.role_id == "admin_daerah") {
          this.getWilayah();
          this.getDaerah();
          this.getCabang();
        }
      }
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  detailDaerah:any;
  getDetailDaerah(id_daerah) {
    this.api.get('org/findone/daerah/'+id_daerah).subscribe(res=>{
      this.detailDaerah = res;
      this.getDetailWilayah(this.detailDaerah.id_prov);
    })
  }

  detailWilayah:any;
  getDetailWilayah(id_prov)
  {
    this.api.get('org/findWilayahByProv/wilayah/'+id_prov).subscribe(res=>{
      this.detailWilayah = res;
      this.dataRanting.id_wilayah = this.detailWilayah.id_wilayah;
      this.getWilayah();
      this.dataRanting.id_daerah = this.userData.kode_area;
      this.getCabang();
    })
  }

  dataWilayah:any = [];
  getWilayah() {
    this.api.get('org/wilayah').subscribe(res=>{
      this.dataWilayah=res;
    })
  }

  dataDaerah:any = [];
  getDaerah() {
    this.api.get('org/daerah?' + 'id_wilayah' + '=' + this.dataRanting.id_wilayah).subscribe((res) => {
      this.dataDaerah=res;
    });
  }

  dataCabang:any = [];
  getCabang() {
    this.api.get('org/cabang?' + 'id_daerah' + '=' + this.dataRanting.id_daerah).subscribe((res) => {
      this.dataCabang=res;
    });
  }

  // detailCabang:any;
  // getDetailCabang() {
  //   this.api.get('org/cabang?' + 'id_cabang' + '=' + this.dataRanting.id_cabang).subscribe((res) => {
  //     this.detailCabang=res[0];
  //     if(this.dataRanting.id_daerah == undefined) {
  //       this.dataRanting.id_daerah = this.detailCabang.id_daerah;
  //       this.getDetailDaerah(this.dataRanting.id_daerah);
  //       this.getCabang();
  //     }
  //   });
  // }

  // detailDaerah:any;
  // getDetailDaerah() {
  //   this.api.get('org/daerah?' + 'id_daerah' + '=' + this.dataRanting.id_daerah).subscribe((res) => {
  //     this.detailDaerah=res[0];
  //     if(this.dataRanting.id_wilayah == undefined) {
  //       this.dataRanting.id_wilayah = this.detailDaerah.id_wilayah;
  //       this.getDaerah();
  //     }
  //   });
  // }

  // detailWilayah:any;
  // getdetailWilayah() {
  //   this.api.get('org/daerah?' + 'id_daerah' + '=' + this.dataRanting.id_daerah).subscribe((res) => {
  //     this.detailWilayah=res[0];
  //     if(this.dataRanting.id_wilayah == undefined) {
  //       this.dataRanting.id_wilayah = this.detailWilayah.id_wilayah;
  //       this.getDaerah();
  //     }
  //   });
  // }

  loading:boolean;
  simpan() {
    this.loading = true;
    if(this.isCreated == true) {
      this.api.post('org/ranting', this.dataRanting).subscribe(res=>{
        this.loading = false;
        alert('Data ranting berhasil ditambahkan.');
        this.dialogRef.close();
      },(err) => {
        this.loading = false;
        alert('Gagal menyimpan data.');
      });
    } else {
      this.api.put('org/ranting/'+this.dataRanting.id_ranting, this.dataRanting).subscribe(res=>{
        this.loading = false;
        alert('Data ranting berhasil diperbarui.');
        this.dialogRef.close();
      },(err) => {
        this.loading = false;
        alert('Gagal memperbarui data.');
      });
    }
  }

  onNoClick(): void {
    this.dialogRef.close();
  }

}

```

##### dialog-ranting.html

```html
<h1 mat-dialog-title>{{isCreated == false ? 'Edit':'Tambah'}} Ranting</h1><div mat-dialog-content>  <mat-form-field class="uk-width-1-1" appearance="outline">    <mat-label>Kode Ranting</mat-label>    <input matInput [(ngModel)]="dataRanting.id_ranting" required #id="ngModel" [disabled]="isCreated == false">  </mat-form-field>  <mat-form-field class="uk-width-1-1" appearance="outline">    <mat-label>Wilayah</mat-label>    <mat-select [(ngModel)]="dataRanting.id_wilayah" required #wilayah="ngModel" [disabled]="dataWilayah.length == 0 || userData.role_id == 'admin_wilayah' || userData.role_id == 'admin_daerah'" (ngModelChange)="getDaerah()">      <mat-option *ngFor="let n of dataWilayah" value="{{n.id_wilayah}}">{{n.nama}}</mat-option>    </mat-select>  </mat-form-field>  <mat-form-field class="uk-width-1-1" appearance="outline">    <mat-label>Daerah</mat-label>    <mat-select [(ngModel)]="dataRanting.id_daerah" required #id_daerah="ngModel" [disabled]="dataDaerah.length == 0 && dataRanting.wilayah == undefined || userData.role_id == 'admin_daerah'" (ngModelChange)="getCabang()">      <mat-option *ngFor="let n of dataDaerah"  value="{{n.id_daerah}}">{{n.nama}}</mat-option>    </mat-select>  </mat-form-field>  <mat-form-field class="uk-width-1-1" appearance="outline">    <mat-label>Cabang</mat-label>    <mat-select [(ngModel)]="dataRanting.id_cabang" required #id_cabang="ngModel" [disabled]="dataRanting.length == 0 && dataRanting.wilayah == undefined && dataRanting.id_daerah == undefined">      <mat-option *ngFor="let n of dataCabang" value="{{n.id_cabang}}">{{n.nama}}</mat-option>    </mat-select>  </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kelurahan</mat-label>      <input matInput [(ngModel)]="dataRanting.nama" required #nama="ngModel">    </mat-form-field>    <!-- <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kantor</mat-label>      <input matInput [(ngModel)]="dataRanting.nama_kantor" required #nama="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Alamat Kantor</mat-label>      <input matInput [(ngModel)]="dataRanting.alamat_kantor" required #nama="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nomor Telepon</mat-label>      <input type="number" [(ngModel)]="dataRanting.phone_kantor" required #telepon="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Email</mat-label>      <input matInput [(ngModel)]="dataRanting.email_kantor" required #email="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama SE</mat-label>      <input matInput [(ngModel)]="dataRanting.nama_se">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>HP SE</mat-label>      <input matInput [(ngModel)]="dataRanting.hp_se">    </mat-form-field> -->    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>No SK</mat-label>      <input matInput [(ngModel)]="dataRanting.no_sk">    </mat-form-field></div><div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Batal</button>  <button mat-raised-button color="primary" [disabled]="loading || id.invalid || wilayah.invalid || id_daerah.invalid || id_cabang.invalid || nama.invalid" (click)="simpan()">{{loading ? 'Mohon tunggu...':'Simpan'}}</button></div>
```


#### Data Wilayah

##### data-wilayah.component.html

```html
<h1 style="padding: 30px; margin: 0px;">Data Wilayah</h1>  <mat-card>    <div class="uk-grid-small" uk-grid>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Cari Wilayah ...</mat-label>        <input matInput (keydown)="applyFilter($event.target.value)" type="search">      </mat-form-field>        <div class="uk-width-expand"></div>        <div>            <button mat-flat-button color="primary" (click)="dialogWilayah(null)" >              Tambah Wilayah            </button>        </div>    </div>    <mat-card-content>      <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">        <ng-container matColumnDef="id_wilayah">          <th mat-sort-header="id_wilayah" mat-header-cell *matHeaderCellDef> Kode Wilayah </th>          <td mat-cell *matCellDef="let element"> {{element.id_wilayah}} </td>        </ng-container>        <ng-container matColumnDef="nama">          <th mat-sort-header="nama" mat-header-cell *matHeaderCellDef> Nama Wilayah </th>          <td mat-cell *matCellDef="let element"> {{element.nama}} </td>        </ng-container>        <ng-container matColumnDef="verified">          <th mat-sort-header="verified" mat-header-cell *matHeaderCellDef> Verifikasi Keberadaan </th>          <td mat-cell *matCellDef="let element">             <mat-icon *ngIf="element.verified == '1'" color="primary">check</mat-icon>           </td>        </ng-container>        <ng-container matColumnDef="no_sk">          <th mat-sort-header="no_sk" mat-header-cell *matHeaderCellDef> No SK </th>          <td mat-cell *matCellDef="let element"> {{element.no_sk}} </td>        </ng-container>        <ng-container matColumnDef="#">            <th mat-header-cell *matHeaderCellDef> # </th>            <td mat-cell *matCellDef="let element; let i=index">                <button mat-icon-button [matMenuTriggerFor]="menu">                    <mat-icon>more_vert</mat-icon>                </button>                <mat-menu #menu="matMenu">                  <button mat-menu-item (click)="dialogWilayah(element)"><mat-icon>create</mat-icon>Edit</button>                  <button mat-menu-item (click)="verification(element)"><mat-icon>check</mat-icon>{{element.verified == '0' ? 'Verifikasi Keberadaan':'Batalkan Verifikasi'}}</button>                                   <button mat-menu-item (click)="hapus(element.id_wilayah)"><mat-icon>delete</mat-icon>Hapus</button>                </mat-menu>            </td>        </ng-container>        <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true;"></tr>        <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>    </table>    <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>    <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card-content>  </mat-card>
```

##### data-wilayah.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DataWilayahComponent } from './data-wilayah.component';describe('DataWilayahComponent', () => {  let component: DataWilayahComponent;  let fixture: ComponentFixture<DataWilayahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DataWilayahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DataWilayahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### data-wilayah.component.ts

```typescript
import { Component, Inject, OnInit, ViewChild } from '@angular/core';import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { ApiService } from 'src/app/services/api.service';import { Sort } from '@angular/material/sort';import { MatTableDataSource } from '@angular/material/table';import { MatPaginator } from '@angular/material/paginator';import { DialogHapusComponent } from "../../dialog-hapus/dialog-hapus.component";import { Router } from '@angular/router';@Component({  selector: 'app-data-wilayah',  templateUrl: './data-wilayah.component.html',  styleUrls: ['./data-wilayah.component.scss']})export class DataWilayahComponent implements OnInit {  sortedData:any = [];  dataSource = new MatTableDataSource(this.sortedData);  displayedColumn: string[] = ['id_wilayah', 'nama', 'verified', 'no_sk','#'];  @ViewChild(MatPaginator) paginator: MatPaginator;  constructor(    public dialog: MatDialog,    public api:ApiService    ) { }  showSpinner: boolean = true;  ngOnInit(): void {    this.getData();  }  data:any=[];  getData()  {    this.api.get('org/wilayah').subscribe(res=>{      this.data=res;      this.sortedData = this.data.slice();      this.dataSource = new MatTableDataSource(this.sortedData);      this.dataSource.paginator = this.paginator;      this.showSpinner = false;    })  }  applyFilter(filterValue: string) {    filterValue = filterValue.trim(); // Remove whitespace    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches    this.dataSource.filter = filterValue;  }    sortData(sort: Sort) {    const data = this.data.slice();    if (!sort.active || sort.direction === '') {      this.sortedData = data;      return;    }    this.sortedData = data.sort((a, b) => {      const isAsc = sort.direction === 'asc';      switch (sort.active) {        case 'id_wilayah': return compare(a.id_wilayah, b.id_wilayah, isAsc);        case 'verified': return compare(a.verified, b.verified, isAsc);        case 'nama': return compare(a.nama, b.nama, isAsc);        default: return 0;      }    });    this.dataSource = new MatTableDataSource(this.sortedData);    this.dataSource.paginator = this.paginator;  }    loading:boolean;  hapus(id) {    const dialogRef = this.dialog.open(DialogHapusComponent, {      width: '450px',      data: {id:id, from: 'wilayah'}    });    dialogRef.afterClosed().subscribe(result => {      this.loading = false;      if(result != undefined) {        alert('Data wilayah berhasil dihapus.');        this.getData();      }    },(err) => {      this.loading = false;      alert('Gagal menghapus data.');    });  }  //DIALOG  dialogWilayah(data): void {    const dialogRef = this.dialog.open(DialogWilayah, {      width: '450px',      data: {        data:data,        semuaWilayah: this.data      }    });    dialogRef.afterClosed().subscribe(result => {      console.log('The dialog was closed');      this.getData();    });  }  verification(data)  {    let putData = data.verified == '1' ? 0 : 1;    this.api.put('org/wilayah/'+data.id_wilayah,{verified:putData}).subscribe(res=>{      //let idx = this.data.map((e)=>{return e.id_wilayah}).indexOf(data.id_wilayah);      data.verified = putData;    })  }}function compare(a: number | string, b: number | string, isAsc: boolean) {  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);}//DIALOG@Component({  selector: 'dialog-container',  templateUrl: 'dialog-wilayah.html',})export class DialogWilayah {  dataWilayah:any = {};  isCreated:boolean;  semuaWilayah:any = [];  kodeTemp:any = '';  constructor(    private router: Router,    public api:ApiService,    public dialogRef: MatDialogRef<DialogWilayah>,    @Inject(MAT_DIALOG_DATA) public sourceData: any  ) {    this.checkRole();    if(sourceData.data != null) {      this.isCreated = false;      this.dataWilayah = sourceData.data;      this.semuaWilayah = sourceData.semuaWilayah;      this.kodeTemp = sourceData.data.id_wilayah;    } else {      this.isCreated = true;    }    this.getProvinsi();  }    userData:any = {};  checkRole()  {    this.api.get('users/me').subscribe(res=>{      this.userData=res;          if(this.userData.role_id == "admin_daerah") {        this.dataWilayah.id_wilayah = this.userData.kode_area;      }    },err=>{      this.router.navigate(['auth/login']);    })  }  dataProvinsi:any = [];  getProvinsi() {    this.api.get('org/wilayah').subscribe(res=>{      this.dataProvinsi=res;      this.dataProvinsi.sort((a, b) => {        let nama_a = a.nama;        let nama_b = b.nama;                if (nama_a < nama_b) return -1;        if (nama_a > nama_b) return 1;                return 0;      })    })  }  already:boolean = false;  checkID() {    if(this.kodeTemp != this.dataWilayah.id_wilayah) {      this.api.get('org/findone/wilayah/'+this.dataWilayah.id_wilayah).subscribe(res=>{        this.check(res);      },(err) => {        this.already = false;        this.loading = false;      });    } else {      this.already = false;    }  }  check(res) {    if(res.status != 'NOT_FOUND') {      this.already = true;    } else {      this.already = false;    }  }  loading:boolean;  simpan() {    this.loading = true;    if(this.isCreated == true) {      this.api.post('org/wilayah', this.dataWilayah).subscribe(res=>{        this.loading = false;        alert('Data wilayah berhasil ditambahkan.');        this.dialogRef.close();      },(err) => {        this.loading = false;        alert('Gagal menyimpan data.');      });    } else {      this.api.put('org/wilayah/'+this.dataWilayah.id_wilayah, this.dataWilayah).subscribe(res=>{        this.loading = false;        alert('Data wilayah berhasil diperbarui.');        this.dialogRef.close();      },(err) => {        this.loading = false;        alert('Gagal memperbarui data.');      });    }  }  onNoClick(): void {    this.dialogRef.close();  }}
```

##### dialog-wilayah.html

```html
<h1 mat-dialog-title>{{isCreated == false ? 'Edit':'Tambah'}} Wilayah</h1><div mat-dialog-content>    <mat-form-field class="uk-width-1-1" appearance="outline">        <mat-label>Kode Wilayah</mat-label>        <input [disabled]="userData.role_id != 'superadmin'" [(ngModel)]="dataWilayah.id_wilayah" (ngModelChange)="checkID()" required #id_wilayah="ngModel" matInput>        <mat-hint *ngIf="already"><mat-error>Kode Wilayah Sudah Digunakan!</mat-error></mat-hint> <br>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Wilayah</mat-label>      <input [(ngModel)]="dataWilayah.nama" required #nama_wilayah="ngModel" matInput>    </mat-form-field>    <!-- <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama Kantor</mat-label>      <input [(ngModel)]="dataWilayah.nama_kantor" required #nama_kantor="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Alamat Kantor</mat-label>      <textarea [(ngModel)]="dataWilayah.alamat_kantor" required #alamat="ngModel" matInput></textarea>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nomor Telepon</mat-label>      <input type="number" [(ngModel)]="dataWilayah.phone_kantor" required #telepon="ngModel" matInput>    </mat-form-field>       <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Email</mat-label>      <input [(ngModel)]="dataWilayah.email_kantor" required #email="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama SE</mat-label>      <input [(ngModel)]="dataWilayah.nama_se" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>HP SE</mat-label>      <input [(ngModel)]="dataWilayah.hp_se" matInput>    </mat-form-field> -->    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>No SK</mat-label>      <input [(ngModel)]="dataWilayah.no_sk" matInput>    </mat-form-field></div><div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Batal</button>  <button mat-raised-button color="primary" [disabled]="loading || id_wilayah.invalid || nama_wilayah.invalid || already" (click)="simpan()">{{loading ? 'Mohon tunggu...':'Simpan'}}</button></div>
```

#### Detail Cabang

##### detail-cabang.component.html

```html
  <ul class="uk-breadcrumb uk-margin-top uk-margin-left">    <li><a href="" routerLink="/public/data-cabang">Data Cabang</a></li>    <li *ngIf="dataCabang != undefined"><span>{{dataCabang.nama}}</span></li>  </ul>  <mat-card>    <div class="uk-grid-small" uk-grid>        <button mat-button color="primary" routerLink="/public/data-cabang">          <mat-icon>arrow_back</mat-icon>          Kembali        </button>        <div class="uk-width-expand"></div>        <div>          <mat-form-field class="uk-width-1-1" appearance="outline">            <mat-label>Cari Ranting ...</mat-label>            <input matInput (keydown)="applyFilter($event.target.value)" type="search">          </mat-form-field>        </div>    </div>    <mat-card-content>        <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">          <ng-container matColumnDef="id_ranting">            <th mat-sort-header="id_ranting" mat-header-cell *matHeaderCellDef> Kode Ranting </th>            <td mat-cell *matCellDef="let element"> {{element.id_ranting}} </td>          </ng-container>          <ng-container matColumnDef="nama_ranting">            <th mat-sort-header="nama_ranting" mat-header-cell *matHeaderCellDef> Nama Ranting </th>            <td mat-cell *matCellDef="let element"> {{element.nama}} </td>          </ng-container>          <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true"></tr>          <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>      </table>      <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>      <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card-content>  </mat-card>
```

##### detail-cabang.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DetailCabangComponent } from './detail-cabang.component';describe('DetailCabangComponent', () => {  let component: DetailCabangComponent;  let fixture: ComponentFixture<DetailCabangComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DetailCabangComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DetailCabangComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### detail-cabang.component.ts

```typescript
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { Component, Inject, OnInit, ViewChild } from '@angular/core';import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';import { ApiService } from 'src/app/services/api.service';import { Sort } from '@angular/material/sort';import { MatTableDataSource } from '@angular/material/table';import { MatPaginator } from '@angular/material/paginator';@Component({  selector: 'app-detail-cabang',  templateUrl: './detail-cabang.component.html',  styleUrls: ['./detail-cabang.component.scss']})export class DetailCabangComponent implements OnInit {  id:any;  sortedData:any = [];  dataSource = new MatTableDataSource(this.sortedData);  displayedColumn: string[] = ['id_ranting', 'nama_ranting'];  @ViewChild(MatPaginator) paginator: MatPaginator;  constructor(    public routes:ActivatedRoute,    public api:ApiService,    public dialog: MatDialog,    private router: Router,  ) {  }  ngOnInit(): void {    this.id=this.routes.snapshot.paramMap.get('id');    this.getDetailCabang();  }  dataCabang:any;  getDetailCabang() {    this.api.get('org/cabang?' + 'id_cabang' + '=' + this.id).subscribe(res=>{      this.dataCabang=res[0];      this.getDataRanting();      })  }  data:any=[];  showSpinner: boolean = true;  getDataRanting()  {    this.api.get('org/ranting?' + 'id_cabang' + '=' + this.id).subscribe(res=>{      this.data=res;      this.sortedData = this.data.slice();      this.dataSource = new MatTableDataSource(this.sortedData);      this.dataSource.paginator = this.paginator;      this.showSpinner = false;    })  }  applyFilter(filterValue: string) {    filterValue = filterValue.trim(); // Remove whitespace    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches    this.dataSource.filter = filterValue;  }  sortData(sort: Sort) {    const data = this.data.slice();    if (!sort.active || sort.direction === '') {      this.sortedData = data;      return;    }    this.sortedData = data.sort((a, b) => {      const isAsc = sort.direction === 'asc';      switch (sort.active) {        case 'id_cabang': return compare(a.id_cabang, b.id_cabang, isAsc);        case 'nama_cabang': return compare(a.nama_cabang, b.nama_cabang, isAsc);        default: return 0;      }    });    this.dataSource = new MatTableDataSource(this.sortedData);    this.dataSource.paginator = this.paginator;  }}function compare(a: number | string, b: number | string, isAsc: boolean) {  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);}
```

#### Detail Daerah

##### detail-daerah.component.html

```html
  <ul class="uk-breadcrumb uk-margin-top uk-margin-left">    <li><a href="" routerLink="/public/data-daerah">Data Daerah</a></li>    <li *ngIf="detailDaerah != undefined"><span>{{detailDaerah.nama}}</span></li>  </ul>  <mat-card>    <div class="uk-grid-small" uk-grid>        <button mat-button color="primary" routerLink="/public/data-daerah">          <mat-icon>arrow_back</mat-icon>          Kembali        </button>        <div class="uk-width-expand"></div>        <div>          <mat-form-field class="uk-width-1-1" appearance="outline">            <mat-label>Cari Cabang ...</mat-label>            <input matInput (keydown)="applyFilter($event.target.value)" type="search">          </mat-form-field>        </div>    </div>    <mat-card-content>        <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">          <ng-container matColumnDef="id_cabang">            <th mat-sort-header="id_cabang" mat-header-cell *matHeaderCellDef> Kode Cabang </th>            <td mat-cell *matCellDef="let element"> {{element.id_cabang}} </td>          </ng-container>          <ng-container matColumnDef="nama_cabang">            <th mat-sort-header="nama_cabang" mat-header-cell *matHeaderCellDef> Nama Cabang </th>            <td mat-cell *matCellDef="let element"> {{element.nama}} </td>          </ng-container>          <ng-container matColumnDef="wilayah">            <th mat-sort-header="wilayah" mat-header-cell *matHeaderCellDef> Wilayah </th>            <td mat-cell *matCellDef="let element"> {{dataWilayah[element.id_wilayah] == undefined ? 'Kosong':dataWilayah[element.id_wilayah].nama}} </td>          </ng-container>          <ng-container matColumnDef="daerah">            <th mat-sort-header="daerah" mat-header-cell *matHeaderCellDef> Daerah </th>            <td mat-cell *matCellDef="let element"> {{dataDaerah[element.id_daerah] == undefined ? 'Kosong':dataDaerah[element.id_daerah].nama}} </td>          </ng-container>          <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true;"></tr>          <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>      </table>      <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>      <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card-content>  </mat-card>
```

##### detail-daerah.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DetailDaerahComponent } from './detail-daerah.component';describe('DetailDaerahComponent', () => {  let component: DetailDaerahComponent;  let fixture: ComponentFixture<DetailDaerahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DetailDaerahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DetailDaerahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### detail-daerah.component.ts

```typescript
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { Component, Inject, OnInit, ViewChild } from '@angular/core';import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';import { ApiService } from 'src/app/services/api.service';import { Sort } from '@angular/material/sort';import { MatTableDataSource } from '@angular/material/table';import { MatPaginator } from '@angular/material/paginator';@Component({  selector: 'app-detail-daerah',  templateUrl: './detail-daerah.component.html',  styleUrls: ['./detail-daerah.component.scss']})export class DetailDaerahComponent implements OnInit {  id:any;  sortedData:any = [];  dataSource = new MatTableDataSource(this.sortedData);  displayedColumn: string[] = ['id_cabang', 'nama_cabang', 'wilayah', 'daerah'];  @ViewChild(MatPaginator) paginator: MatPaginator;  constructor(    public routes:ActivatedRoute,    public api:ApiService,    public dialog: MatDialog,    private router: Router,  ) {  }  ngOnInit(): void {    this.id=this.routes.snapshot.paramMap.get('id');    this.getWilayah();    this.getDaerah();    this.getDetailDaerah();  }  dataWilayah:any = {};  getWilayah() {    this.api.get('org/wilayah').subscribe(res=>{      this.parseWilayah(res);    });  }  parseWilayah(res) {    for(var i=0; i<res.length; i++) {      const idx = res[i].id_wilayah;      if(this.dataWilayah[idx] == undefined )      {        this.dataWilayah[idx] = res[i];      }    }  }  dataDaerah:any = {};  getDaerah() {    this.api.get('org/daerah').subscribe(res=>{      this.parseDaerah(res);    });  }  parseDaerah(res) {    for(var i=0; i<res.length; i++) {      const idx = res[i].id_daerah;      if(this.dataDaerah[idx] == undefined )      {        this.dataDaerah[idx] = res[i];      }    }  }  detailDaerah:any;  getDetailDaerah() {    this.api.get('org/daerah?' + 'id_daerah' + '=' + this.id).subscribe(res=>{      this.detailDaerah=res[0];      this.getDataCabang();      })  }  data:any=[];  showSpinner: boolean = true;  getDataCabang()  {    this.api.get('org/cabang?' + 'id_daerah' + '=' + this.id).subscribe(res=>{      this.data=res;      this.sortedData = this.data.slice();      this.dataSource = new MatTableDataSource(this.sortedData);      this.dataSource.paginator = this.paginator;      this.showSpinner = false;    })  }  applyFilter(filterValue: string) {    filterValue = filterValue.trim(); // Remove whitespace    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches    this.dataSource.filter = filterValue;  }  sortData(sort: Sort) {    const data = this.data.slice();    if (!sort.active || sort.direction === '') {      this.sortedData = data;      return;    }    this.sortedData = data.sort((a, b) => {      const isAsc = sort.direction === 'asc';      switch (sort.active) {        case 'id_cabang': return compare(a.id_cabang, b.id_cabang, isAsc);        case 'nama_cabang': return compare(a.nama_cabang, b.nama_cabang, isAsc);        case 'daerah': return compare(a.daerah, b.daerah, isAsc);        default: return 0;      }    });    this.dataSource = new MatTableDataSource(this.sortedData);    this.dataSource.paginator = this.paginator;  }}function compare(a: number | string, b: number | string, isAsc: boolean) {  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);}
```

#### Detail Ranting

##### detail-ranting.component.html

```html
  <ul class="uk-breadcrumb uk-margin-left uk-margin-top">    <li><a href="" routerLink="/public/data-ranting">Data Ranting</a></li>    <li *ngIf="detailRanting != undefined"><span>{{detailRanting.nama}}</span></li>  </ul>  <mat-card>    <div class="uk-grid-small" uk-grid>      <button mat-button color="primary" routerLink="/public/data-ranting">        <mat-icon>arrow_back</mat-icon>        Kembali      </button>        <div class="uk-width-expand"></div>        <div>            <button mat-flat-button color="primary" (click)="dialogAnggota(null)" >              Tambah Anggota            </button>        </div>    </div>        <mat-card-content>      <tabel-anggota [dataAnggota]="dataSource"></tabel-anggota>      <!-- <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="anggota-table">        <ng-container matColumnDef="no">          <th mat-sort-header="no" mat-header-cell *matHeaderCellDef> No. </th>          <td mat-cell *matCellDef="let i=index"> {{this.paginator.pageIndex == 0 ? i + 1 : 1 + i + this.paginator.pageIndex * this.paginator.pageSize}} </td>        </ng-container>        <ng-container matColumnDef="foto">          <th mat-sort-header="foto" mat-header-cell *matHeaderCellDef> Foto </th>          <td mat-cell *matCellDef="let element">            <div class="image-uploader">              <div class="overlay">              </div>              <button mat-icon-button mat-button (click)="dialogUploadFoto(element.id)" >                  <mat-icon>cloud_upload</mat-icon>              </button>              <img src="{{element.foto == null ? 'assets/imgs/no-image.jpg':serverUrl+'photos/anggota/'+element.foto}}" style="width: 100px;">            </div>          </td>        </ng-container>        <ng-container matColumnDef="no_ktaa">          <th mat-sort-header="no_ktaa" mat-header-cell *matHeaderCellDef> No KTAA </th>          <td mat-cell *matCellDef="let element"> {{element.nba}} </td>        </ng-container>        <ng-container matColumnDef="nama_anggota">          <th mat-sort-header="nama_anggota" mat-header-cell *matHeaderCellDef> Nama Anggota </th>          <td mat-cell *matCellDef="let element"> {{element.nama}} </td>        </ng-container>        <ng-container matColumnDef="alamat">          <th mat-sort-header="alamat" mat-header-cell *matHeaderCellDef> Alamat </th>          <td mat-cell *matCellDef="let element"> {{element.alamat}} </td>        </ng-container>        <ng-container matColumnDef="#">            <th mat-header-cell *matHeaderCellDef> # </th>            <td mat-cell *matCellDef="let element; let i=index">                <button mat-icon-button [matMenuTriggerFor]="menu">                    <mat-icon>more_vert</mat-icon>                </button>                <mat-menu #menu="matMenu">                  <button mat-menu-item (click)="dialogAnggota(element)" ><mat-icon>create</mat-icon>Detail Data Anggota</button>                  <button mat-menu-item (click)="dialogKartu(element)" ><mat-icon>card_membership</mat-icon>Lihat Kartu Anggota</button>                  <button mat-menu-item (click)="hapus(element.id)"><mat-icon>delete</mat-icon>Hapus Data Anggota</button>                </mat-menu>            </td>        </ng-container>        <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true"></tr>        <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>    </table>    <mat-paginator #paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator> -->    </mat-card-content>  </mat-card>
```

#####  detail-ranting.component.spec.ts

```typescrit
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DetailRantingComponent } from './detail-ranting.component';describe('DetailRantingComponent', () => {  let component: DetailRantingComponent;  let fixture: ComponentFixture<DetailRantingComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DetailRantingComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DetailRantingComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### detail-ranting.component.ts

```typescript
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { Component, Inject, OnInit, ViewChild } from '@angular/core';
import { ImageUploaderComponent } from '../../../media/image-uploader/image-uploader.component';
import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';
import { ApiService } from 'src/app/services/api.service';
import { Sort } from '@angular/material/sort';
import { MatTableDataSource } from '@angular/material/table';
import { MatPaginator } from '@angular/material/paginator';
import * as pdfmake from 'pdfmake';
import html2canvas from 'html2canvas'; 
import jspdf from 'jspdf';
import { DialogHapusComponent } from '../../dialog-hapus/dialog-hapus.component';
import * as htmlToImage from 'html-to-image';
import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image';
import { DateAdapter } from '@angular/material/core';

@Component({
  selector: 'app-detail-ranting',
  templateUrl: './detail-ranting.component.html',
  styleUrls: ['./detail-ranting.component.scss']
})
export class DetailRantingComponent implements OnInit {

  id:any;
  sortedData:any = [];
  dataSource = new MatTableDataSource(this.sortedData);
  displayedColumn: string[] = ['no', 'foto', 'no_ktaa', 'nama_anggota', 'alamat','#'];
  @ViewChild(MatPaginator) paginator: MatPaginator;
  constructor(
    public routes:ActivatedRoute,
    public api:ApiService,
    public dialog: MatDialog,
    private router: Router,
    private dateAdapter: DateAdapter<Date>
  ) {
    this.dateAdapter.setLocale('en-GB');
  }

  serverUrl='https://apis.ruang-ekspresi.id/ppa/';
  ngOnInit(): void {
    this.id=this.routes.snapshot.paramMap.get('id');
    this.getDetailRanting();
  }

  detailRanting:any;
  getDetailRanting() {
    this.api.get('org/ranting/'+ this.id).subscribe(res=>{
      this.detailRanting=res[0];
      this.getDataAnggota();
    })
  }

  data:any=[];
  showSpinner: boolean = true;
  getDataAnggota()
  {
    this.api.get('org/anggotaRan?' + 'id_ranting' + '=' + this.id).subscribe(res=>{
      this.data=res;
      this.sortedData = this.data.slice();
      this.dataSource = new MatTableDataSource(this.sortedData);
      this.dataSource.paginator = this.paginator;
    }, error => {
      this.data = [];
    })
  }

  applyFilter(filterValue: string) {
    filterValue = filterValue.trim(); // Remove whitespace
    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches
    this.dataSource.filter = filterValue;
  }

  sortData(sort: Sort) {
    const data = this.data.slice();
    if (!sort.active || sort.direction === '') {
      this.sortedData = data;
      return;
    }

    this.sortedData = data.sort((a, b) => {
      const isAsc = sort.direction === 'asc';
      switch (sort.active) {
        case 'no': return compare(a.no, b.no, isAsc);
        case 'no_ktaa': return compare(a.no_ktaa, b.no_ktaa, isAsc);
        case 'nama_anggota': return compare(a.nama_anggota, b.nama_anggota, isAsc);
        case 'alamat': return compare(a.alamat, b.alamat, isAsc);
        default: return 0;
      }
    });
    this.dataSource = new MatTableDataSource(this.sortedData);
    this.dataSource.paginator = this.paginator;
  }

  //DIALOG
  dialogAnggota(dt): void {
    const dialogRef = this.dialog.open(DialogAnggota, {
      width: '1000px',
      height: 'auto',
      data: {data:dt, detailRanting: this.detailRanting}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      this.data = [];
      this.getDetailRanting();
    });
  }

  //DIALOG KARTU ANGGOTA
  dialogKartu(data): void {
    const dialogRef = this.dialog.open(DialogKartuAnggota, {
      width: '600px',
      height: '400px',
      data: {data:data}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      this.data = [];
      this.getDetailRanting();
    });
  }

  //DIALOG UPLOAD FOTO
  dialogUploadFoto(id): void {
    const dialogRef = this.dialog.open(ImageUploaderComponent, {
      width: '500px',
      data: {id:id}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      this.getDetailRanting();
    });
  }

  loading:boolean;
  hapus(id) {
    const dialogRef = this.dialog.open(DialogHapusComponent, {
      width: '450px',
      data: {id:id, from: 'anggota'}
    });
    dialogRef.afterClosed().subscribe(result => {
      this.loading = false;
      if(result != undefined) {
        alert('Data anggota berhasil dihapus.');
        this.getDetailRanting();
      }
    },(err) => {
      this.loading = false;
      alert('Gagal menghapus data.');
    });
  }

}

function compare(a: number | string, b: number | string, isAsc: boolean) {
  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);
}

//DIALOG
@Component({
  selector: 'dialog-container',
  templateUrl: 'dialog-anggota.html',
})
export class DialogAnggota {

  dataAnggota:any = {};
  semua_anggota:any = [];
  detailRanting:any = {};
  isCreated:boolean;
  constructor(
    public api:ApiService,
    public dialogRef: MatDialogRef<DialogAnggota>,
    @Inject(MAT_DIALOG_DATA) public sourceData: any
  ) {
    this.getSemuaAnggota();
    this.detailRanting = sourceData.detailRanting;
    if(this.dataRanting != undefined) {
      this.getDetailCab();
    }
    if(sourceData.data != undefined) {    
      this.dataAnggota = sourceData.data;
      this.isCreated = false;
      //get alamat & wilayah
      this.getWilayah();
      this.getDaerah();
      this.getCabang();
      this.getRanting();
    } else {
      this.isCreated = true;
      this.getWilayah();
    }
  }

  detailCab:any = {};
  getDetailCab() {
    this.api.get('org/cabang/'+ this.detailRanting.id_cabang).subscribe(res=>{
      this.detailCab=res[0];
      this.getDetailDaerah();
    })
  }

  detailDaerah:any = {};
  getDetailDaerah() {
    this.api.get('org/daerah/'+ this.detailCab.id_daerah).subscribe(res=>{
      this.detailDaerah=res[0];
      if(this.detailDaerah != undefined) {
        this.dataAnggota.ranting = this.detailRanting.id;
        this.dataAnggota.cabang = this.detailRanting.id_cabang;
        this.dataAnggota.daerah = this.detailDaerah.id;
        this.dataAnggota.wilayah = this.detailDaerah.kode_wilayah;
        this.getDaerah();
        this.getCabang();
        this.getRanting();
      }
    })
  }

  getSemuaAnggota()
  {
    this.api.get('anggota/data_anggota').subscribe(res=>{
      this.semua_anggota=res;
    })
  }

  //wilayah ppa
  dataWilayah:any=[];
  getWilayah() {
    //bersihkan child
    this.dataDaerah = [];
    this.dataCabang = [];
    this.dataRanting = [];

    this.api.get('org/wilayah').subscribe(res=>{
      this.dataWilayah=res;
    });
  }

  

  dataDaerah:any=[];
  getDaerah() {
    //bersihkan child
    this.dataDaerah = [];
    this.dataCabang = [];
    this.dataRanting = [];

    this.api.get('org/daerah?' + 'id_wilayah' + '=' + this.dataAnggota.wilayah).subscribe(res=>{
      this.dataDaerah = res;
    })
  }

  dataCabang:any=[];
  getCabang() {
    //bersihkan child
    this.dataCabang = [];
    this.dataRanting = [];
    this.api.get('org/cabang?' + 'id_daerah' + '=' + this.dataAnggota.daerah).subscribe(res=>{
      this.dataCabang = res;
    })
  }

  dataRanting:any=[];
  getRanting() {
    this.api.get('org/ranting?' + 'id_cabang' + '=' + this.dataAnggota.cabang).subscribe(res=>{
      this.dataRanting = res;
    }, error => {
      this.dataRanting = [];
    })
  }

  loading:boolean;
  simpan() {
    this.loading = true;
    if(this.isCreated == true) {
      this.dataAnggota.created_at = new Date();
      var dataTerakhir = this.semua_anggota.reduce((max, data) => max.id > data.id ? max : data);
      this.dataAnggota.nba = new Date().getFullYear()+this.dataAnggota.wilayah+this.dataAnggota.daerah+this.dataAnggota.cabang+this.dataAnggota.ranting+(Number(dataTerakhir.id)+1);
      let cek =  this.semua_anggota.find(x => x.nba == this.dataAnggota.nba);
      if(cek == undefined) {
        this.api.post('anggota/data_anggota', this.dataAnggota).subscribe(res=>{
          this.loading = false;
          alert('Data anggota berhasil ditambahkan.');
          this.dialogRef.close();
        },(err) => {
          this.loading = false;
          alert('Gagal menambahkan data.');
        });
      } else {
        alert('Nomor KTAA sudah terdaftar, ulangi kembali.');
      }
    } else {
      this.api.put('anggota/data_anggota/'+this.dataAnggota.id, this.dataAnggota).subscribe(res=>{
        this.loading = false;
        alert('Data anggota berhasil diperbarui.');
        this.dialogRef.close();
      },(err) => {
        this.loading = false;
        alert('Gagal memperbarui data.');
      });
    }
  }

  cetak(data) {
    console.log(data)
  }

  onNoClick(): void {
    this.dialogRef.close();
  }

}

//DIALOG KARTU ANGGOTA
@Component({
  selector: 'dialog-container',
  templateUrl: 'dialog-kartu-anggota.html',
  styleUrls: ['./detail-ranting.component.scss']
})
export class DialogKartuAnggota {

  dataAnggota:any = {};
  isCreated:boolean;
  loading:boolean;
  
  margin = 2;
  marginTop = 2;
  marginBottom = 5;
  marginLeft = 10;
  marginRight = 10;
  width = 2;
  height = 30;
  background = '#c80e3d';
  lineColor = 'black';

  serverUrl='https://apis.ruang-ekspresi.id/ppa/';
  constructor(
    public api:ApiService,
    public dialogRef: MatDialogRef<DialogKartuAnggota>,
    @Inject(MAT_DIALOG_DATA) public sourceData: any
  ) {
    if(sourceData.data != undefined) {    
      this.dataAnggota = sourceData.data;
      this.isCreated = false;
    } else {
      this.isCreated = true;
    }
  }

  async cetak() {
    // html2canvas(document.getElementById('kartu_anggota')).then(async function (canvas) {
    //   document.body.appendChild(canvas);
    //   var data = canvas.toDataURL('image/jpeg', 1.0);
    //   var docDefinition = {
    //       content: [{
    //           image: data
    //       }]
    //   };
    //   await pdfmake.createPdf(docDefinition).open();
    // });
    var container = document.getElementById("kartu_anggota");; // full page 
		html2canvas(container).then(function(canvas) {
		
			var link = document.createElement("a");
			document.body.appendChild(link);
			link.download = "kartu_anggota.png";
			link.href = canvas.toDataURL("image/png");
			link.target = '_blank';
			link.click();
		});
    // let data = document.getElementById('kartu_anggota');  
    // html2canvas(data).then(canvas => {
    //   const contentDataURL = canvas.toDataURL('image/png')  
    //   let pdf = new jspdf('l', 'cm', 'a4'); //Generates PDF in landscape mode
    //   // let pdf = new jspdf('p', 'cm', 'a4'); Generates PDF in portrait mode
    //   pdf.addImage(contentDataURL, 'PNG', 0, 0, 29.7, 21.0);  
    //   pdf.save('kartu_anggota.pdf');   
    // }); 
  }

  onNoClick(): void {
    this.dialogRef.close();
  }
}

```

##### dialog-anggota.html

```html
<!-- <h1 mat-dialog-title>{{isCreated == false ? 'Edit':'Tambah'}} Anggota</h1> --><div mat-dialog-content>    <!-- <h2>Data Pribadi</h2>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nama</mat-label>      <input matInput required [(ngModel)]="dataAnggota.nama" #nama="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Email</mat-label>      <input matInput required [(ngModel)]="dataAnggota.email" type="email" #email="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Nomor HP</mat-label>      <input matInput required [(ngModel)]="dataAnggota.no_telp" #no_telp="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Tempat Lahir</mat-label>      <input type="text" required [(ngModel)]="dataAnggota.tempat_lahir" matInput #tempat_lahir="ngModel">    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Tanggal Lahir</mat-label>      <input matInput required [(ngModel)]="dataAnggota.tanggal_lahir" #tanggal_lahir="ngModel" [matDatepicker]="birthDay">      <mat-datepicker-toggle matSuffix [for]="birthDay"></mat-datepicker-toggle>      <mat-datepicker #birthDay></mat-datepicker>    </mat-form-field>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>RT</mat-label>        <input type="number" required [(ngModel)]="dataAnggota.rt" #rt="ngModel" matInput>      </mat-form-field>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>RW</mat-label>        <input type="number" required [(ngModel)]="dataAnggota.rw" #rw="ngModel" matInput>      </mat-form-field>    </div>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>Provinsi</mat-label>        <mat-select required [(ngModel)]="dataAnggota.provinsi" #provinsi="ngModel" [disabled]="dataProvinsi.length == 0" (ngModelChange)="getKabupaten()">          <mat-option *ngIf="dataProvinsi.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>          <mat-option *ngFor="let n of dataProvinsi"  value="{{n.id_prov}}">{{n.nama}}</mat-option>        </mat-select>      </mat-form-field>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>Kabupaten/Kota</mat-label>        <mat-select required [(ngModel)]="dataAnggota.kabupaten" #kabupaten="ngModel" [disabled]="dataAnggota.provinsi == undefined" (ngModelChange)="getKecamatan()">          <mat-option *ngIf="dataKabupaten.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>          <mat-option *ngFor="let n of dataKabupaten" value="{{n.id_kab}}">{{n.nama_kabupaten}}</mat-option>        </mat-select>      </mat-form-field>    </div>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>Kecamatan</mat-label>        <mat-select required [(ngModel)]="dataAnggota.kecamatan" #kecamatan="ngModel" [disabled]="dataAnggota.kabupaten == undefined" (ngModelChange)="getKelurahan()">          <mat-option *ngIf="dataKecamatan.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>          <mat-option *ngFor="let n of dataKecamatan"  value="{{n.id_kec}}">{{n.nama_kecamatan}}</mat-option>        </mat-select>      </mat-form-field>      <mat-form-field class="uk-width-1-2" appearance="outline">        <mat-label>Desa/Kelurahan</mat-label>        <mat-select required [(ngModel)]="dataAnggota.desa" #desa="ngModel" [disabled]="dataAnggota.kecamatan == undefined">          <mat-option *ngIf="dataKelurahan.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>          <mat-option *ngFor="let n of dataKelurahan"  value="{{n.id_kel}}">{{n.nama_kelurahan}}</mat-option>        </mat-select>      </mat-form-field>    </div>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Alamat</mat-label>      <textarea matInput required [(ngModel)]="dataAnggota.alamat" #alamat="ngModel"></textarea>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Status</mat-label>      <mat-select required [(ngModel)]="dataAnggota.status" #status="ngModel">        <mat-option *ngFor="let n of ['Menikah','Belum Menikah','Duda/Janda']"  value="{{n}}">{{n}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Pekerjaan</mat-label>      <input matInput [(ngModel)]="dataAnggota.pekerjaan" #pekerjaan="ngModel">    </mat-form-field>    <h2>Pendidikan</h2>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-3-4" appearance="outline">        <mat-label>SD/MI</mat-label>        <input [(ngModel)]="dataAnggota.pend_sd" #pend_sd="ngModel" matInput>      </mat-form-field>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Tahun Lulus</mat-label>        <input type="number" [(ngModel)]="dataAnggota.lulus_sd" #lulus_sd="ngModel" matInput>      </mat-form-field>    </div>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-3-4" appearance="outline">        <mat-label>SMP/MTs</mat-label>        <input [(ngModel)]="dataAnggota.pend_smp" #pend_smp="ngModel" matInput>      </mat-form-field>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Tahun Lulus</mat-label>        <input type="number" [(ngModel)]="dataAnggota.lulus_smp" #lulus_smp="ngModel" matInput>      </mat-form-field>    </div>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-3-4" appearance="outline">        <mat-label>SMA/MA</mat-label>        <input [(ngModel)]="dataAnggota.pend_sma" #pend_sma="ngModel" matInput>      </mat-form-field>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Tahun Lulus</mat-label>        <input type="number" [(ngModel)]="dataAnggota.lulus_sma" #lulus_sma="ngModel" matInput>      </mat-form-field>    </div>    <div class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s" uk-grid>      <mat-form-field class="uk-width-3-4" appearance="outline">        <mat-label>S1</mat-label>        <input [(ngModel)]="dataAnggota.pend_s1" #pend_s1="ngModel" matInput>      </mat-form-field>      <mat-form-field class="uk-width-1-4" appearance="outline">        <mat-label>Tahun Lulus</mat-label>        <input type="number" [(ngModel)]="dataAnggota.lulus_s1" #lulus_s1="ngModel" matInput>      </mat-form-field>    </div>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Gelar Kesarjanaan yang lain</mat-label>      <input [(ngModel)]="dataAnggota.gelar" #gelar="ngModel" matInput>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Pendidikan Non Formal</mat-label>      <input [(ngModel)]="dataAnggota.gelar_non" #gelar_non="ngModel" matInput>    </mat-form-field>    <h2>Keanggotaan Aisyiyah</h2>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Wilayah</mat-label>      <mat-select required [(ngModel)]="dataAnggota.wilayah" #wilayah="ngModel" [disabled]="dataWilayah.length == 0" (ngModelChange)="getDaerah()">        <mat-option *ngIf="dataWilayah.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>        <mat-option *ngFor="let n of dataWilayah"  value="{{n.id}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Daerah</mat-label>      <mat-select required [(ngModel)]="dataAnggota.daerah" #daerah="ngModel" [disabled]="dataDaerah.length == 0 && dataAnggota.wilayah == undefined" (ngModelChange)="getCabang()">        <mat-option *ngIf="dataDaerah.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>        <mat-option *ngFor="let n of dataDaerah"  value="{{n.id}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Cabang</mat-label>      <mat-select required [(ngModel)]="dataAnggota.cabang" #cabang="ngModel" [disabled]="dataCabang.length == 0 && dataAnggota.daerah == undefined" (ngModelChange)="getRanting()">        <mat-option *ngIf="dataCabang.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>        <mat-option *ngFor="let n of dataCabang"  value="{{n.id}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field>    <mat-form-field class="uk-width-1-1" appearance="outline">      <mat-label>Ranting</mat-label>      <mat-select required [(ngModel)]="dataAnggota.ranting" #ranting="ngModel" [disabled]="dataRanting.length == 0">        <mat-option *ngIf="dataRanting.length == 0" placeholder="Sedang memuat data..">Sedang memuat data...</mat-option>        <mat-option *ngFor="let n of dataRanting" value="{{n.id}}">{{n.nama}}</mat-option>      </mat-select>    </mat-form-field> -->    <form-anggota [buttonText]="'Simpan'"></form-anggota>   </div><!-- <div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-flat-button (click)="onNoClick()">Batal</button>  <button mat-flat-button [disabled]="loading" (click)="cetak(dataAnggota)">Cetak PDF</button>  <button mat-flat-button color="primary" [disabled]="loading || ranting.invalid || cabang.invalid || daerah.invalid || wilayah.invalid || gelar_non.invalid ||  gelar.invalid || lulus_s1.invalid || pend_s1.invalid || lulus_sma.invalid || pend_sma.invalid || lulus_smp.invalid || pend_smp.invalid || daerah.invalid || lulus_sd.invalid || pend_sd.invalid ||  pekerjaan.invalid || status.invalid || alamat.invalid || desa.invalid || kecamatan.invalid || kabupaten.invalid || provinsi.invalid || rt.invalid || rw.invalid || tanggal_lahir.invalid ||  tempat_lahir.invalid || no_telp.invalid || email.invalid || nama.invalid" (click)="simpan()">{{loading ? 'Mohon tunggu...':'Simpan'}}</button></div> -->
```

##### dialog-kartu-anggota.html

```html
<h1 mat-dialog-title>Kartu Tanda Anggota</h1><div mat-dialog-content>    <div id="kartu_anggota" class='business-card'>        <div class='bc__logo'>            <img src="assets/imgs/image.png" width="20%" style="margin-top: -10px; margin-left: 15px; color:#ffffff;">            <h2>PP Aisyiyah</h2>        </div>        <img src="{{dataAnggota.foto == undefined ? 'assets/imgs/no-image.jpg':serverUrl+'photos/anggota/'+dataAnggota.foto}}" width="15%" style="margin-top: 150px; margin-left: 25px;">         <div class='bc__tagline'>            <p style="text-align: left; margin-left: 10px;">Nama : {{dataAnggota.nama}}<br>            NBA : {{dataAnggota.nba}}</p>            <ngx-barcode [bc-value]="dataAnggota.nba"                 [bc-display-value]="false" [bc-width]="width" [bc-height]="height"                [bc-margin]="margin" [bc-margin-top]="marginTop" [bc-margin-bottom]="marginBottom"                 [bc-margin-left]="marginLeft" [bc-background]="background" [bc-line-color]="lineColor" [bc-margin-right]="marginRight"></ngx-barcode>        </div>    </div></div><div style="margin-top: 260px;" mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Tutup</button>  <button mat-raised-button color="primary" [disabled]="loading" (click)="cetak()">{{loading ? 'Mohon tunggu...':'Cetak'}}</button></div>
```

#### Detail Wilayah

##### detail-wilayah.component.html

```html
  <ul class="uk-breadcrumb uk-margin-top uk-margin-left">    <li><a href="" routerLink="/public/data-wilayah">Data Wilayah</a></li>    <li *ngIf="detailWilayah != undefined"><span>{{detailWilayah.nama}}</span></li>  </ul>  <mat-card>    <div class="uk-grid-small" uk-grid>        <button mat-button color="primary" routerLink="/public/data-wilayah">          <mat-icon>arrow_back</mat-icon>          Kembali        </button>        <div class="uk-width-expand"></div>        <div>          <mat-form-field class="uk-width-1-1" appearance="outline">            <mat-label>Cari Daerah ...</mat-label>            <input matInput (keydown)="applyFilter($event.target.value)" type="search">          </mat-form-field>        </div>    </div>    <mat-card-content>        <table mat-table matSort [dataSource]="dataSource" (matSortChange)="sortData($event)" class="table-strech">          <ng-container matColumnDef="id_daerah">            <th mat-sort-header="id_daerah" mat-header-cell *matHeaderCellDef> ID Daerah </th>            <td mat-cell *matCellDef="let element"> {{element.id_daerah}} </td>          </ng-container>          <ng-container matColumnDef="nama_daerah">            <th mat-sort-header="nama_daerah" mat-header-cell *matHeaderCellDef> Nama Daerah </th>            <td mat-cell *matCellDef="let element"> {{element.nama}} </td>          </ng-container>          <ng-container matColumnDef="wilayah">            <th mat-sort-header="wilayah" mat-header-cell *matHeaderCellDef> Wilayah </th>            <td mat-cell *matCellDef="let element"> {{detailWilayah.nama}} </td>          </ng-container>          <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true;"></tr>          <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>      </table>      <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>      <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card-content>  </mat-card>
```

##### detail-wilayah.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DetailWilayahComponent } from './detail-wilayah.component';describe('DetailWilayahComponent', () => {  let component: DetailWilayahComponent;  let fixture: ComponentFixture<DetailWilayahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DetailWilayahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DetailWilayahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### detail-wilayah.component.ts

```typescript
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { Component, Inject, OnInit, ViewChild } from '@angular/core';import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';import { ApiService } from 'src/app/services/api.service';import { Sort } from '@angular/material/sort';import { MatTableDataSource } from '@angular/material/table';import { MatPaginator } from '@angular/material/paginator';@Component({  selector: 'app-detail-wilayah',  templateUrl: './detail-wilayah.component.html',  styleUrls: ['./detail-wilayah.component.scss']})export class DetailWilayahComponent implements OnInit {  id:any;  sortedData:any = [];  dataSource = new MatTableDataSource(this.sortedData);  displayedColumn: string[] = ['id_daerah', 'nama_daerah', 'wilayah'];  @ViewChild(MatPaginator) paginator: MatPaginator;  constructor(    public routes:ActivatedRoute,    public api:ApiService,    public dialog: MatDialog,    private router: Router,  ) {  }  ngOnInit(): void {    this.id=this.routes.snapshot.paramMap.get('id');    this.getDetailWilayah(this.id);  }  detailWilayah:any;  getDetailWilayah(id) {    this.api.get('org/wilayah?' + 'id_wilayah' + '=' + id).subscribe(res=>{      this.detailWilayah=res[0];      this.getDaerah();    })  }  data:any=[];  showSpinner: boolean = true;  getDaerah() {    this.api.get('org/daerah?' + 'id_wilayah' + '=' + this.id).subscribe(res=>{      this.data=res;      this.sortedData = this.data.slice();      this.dataSource = new MatTableDataSource(this.sortedData);      this.dataSource.paginator = this.paginator;      this.showSpinner = false;    });  }  applyFilter(filterValue: string) {    filterValue = filterValue.trim(); // Remove whitespace    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches    this.dataSource.filter = filterValue;  }  sortData(sort: Sort) {    const data = this.data.slice();    if (!sort.active || sort.direction === '') {      this.sortedData = data;      return;    }    this.sortedData = data.sort((a, b) => {      const isAsc = sort.direction === 'asc';      switch (sort.active) {        case 'id_cabang': return compare(a.id_cabang, b.id_cabang, isAsc);        case 'nama_cabang': return compare(a.nama_cabang, b.nama_cabang, isAsc);        default: return 0;      }    });    this.dataSource = new MatTableDataSource(this.sortedData);    this.dataSource.paginator = this.paginator;  }}function compare(a: number | string, b: number | string, isAsc: boolean) {  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);}
```


#### Dialog Daerah

##### dialog-daerah.component.html

```html
<h2 mat-dialog-title>Daftar Daerah</h2><div mat-dialog-content>  <div class="uk-flex uk-margin uk-flex-middle uk-text-center">    <div class="uk-flex uk-flex-middle uk-width-expand">        <span class="uk-width-expand"></span>        <div class="uk-width-1-1@m">            <mat-form-field class="uk-width-1-1" appearance="outline">                <mat-label>Cari Daerah</mat-label>                <input matInput [(ngModel)]="searchTerm" (keydown)="applyFilter($event)" placeholder="Masukkan Nama" type="search">            </mat-form-field>        </div>    </div>     </div>    <mat-selection-list [multiple]="false">        <mat-list-option *ngFor="let n of dataDaerah" [value]="n.kode_wilayah" (click)="pilihDaerah(n)">          {{n.nama}}        </mat-list-option>    </mat-selection-list>        <mat-spinner *ngIf="dataDaerah.length == 0"></mat-spinner></div>
```

##### dialog-daerah.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DialogDaerahComponent } from './dialog-daerah.component';describe('DialogDaerahComponent', () => {  let component: DialogDaerahComponent;  let fixture: ComponentFixture<DialogDaerahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DialogDaerahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DialogDaerahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### dialog-daerah.component.ts

```typescript
import { Component, OnInit } from '@angular/core';import { MatDialogRef } from '@angular/material/dialog';import { ApiService } from 'src/app/services/api.service';@Component({  selector: 'app-dialog-daerah',  templateUrl: './dialog-daerah.component.html',  styleUrls: ['./dialog-daerah.component.scss']})export class DialogDaerahComponent implements OnInit {  constructor(    private api: ApiService,    public dialogRef : MatDialogRef<DialogDaerahComponent>  ) { }  ngOnInit(): void {    this.getDataDaerah();  }  dataDaerah: any = [];  dataDaerahTemp: any = [];  getDataDaerah()  {    this.dataDaerah = [];    this.dataDaerahTemp = [];    this.api.get('org/daerah').subscribe(res=>{      this.dataDaerah = res;      this.dataDaerahTemp = res;    })  }  initializeItems(): void {    this.dataDaerah = this.dataDaerahTemp;  }  searchTerm: string = '';  applyFilter(evt) {    this.initializeItems();    const searchTerm = evt.srcElement.value;    if (!searchTerm) {      return;    }    this.dataDaerah = this.dataDaerah.filter(daerah => {      if (daerah.nama && searchTerm) {        if (daerah.nama.toLowerCase().indexOf(searchTerm.toLowerCase()) > -1) {          return true;        }        return false;      }    });  }  pilihDaerah(data)  {    this.dialogRef.close(data);  }}
```

#### Dialog Wilayah

##### dialog-wilayah.component.html

```html
<h2 mat-dialog-title>Daftar Wilayah</h2><div mat-dialog-content>  <div class="uk-flex uk-margin uk-flex-middle uk-text-center">    <div class="uk-flex uk-flex-middle uk-width-expand">        <span class="uk-width-expand"></span>        <div class="uk-width-1-1@m">            <mat-form-field class="uk-width-1-1" appearance="outline">                <mat-label>Cari Wilayah</mat-label>                <input matInput [(ngModel)]="searchTerm" (keydown)="applyFilter($event)" placeholder="Masukkan Nama" type="search">            </mat-form-field>        </div>    </div>   </div>  <mat-selection-list [multiple]="false">      <mat-list-option *ngFor="let n of dataWilayah" [value]="n.wilayah" (click)="pilih(n)">        {{n.nama}}      </mat-list-option>  </mat-selection-list>    <mat-spinner *ngIf="dataWilayah.length == 0"></mat-spinner></div>
```

##### dialog-wilayah.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DialogWilayahComponent } from './dialog-wilayah.component';describe('DialogWilayahComponent', () => {  let component: DialogWilayahComponent;  let fixture: ComponentFixture<DialogWilayahComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DialogWilayahComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DialogWilayahComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### dialog-wilayah.component.ts

```typescript
import { Component, OnInit } from '@angular/core';import { MatDialogRef } from '@angular/material/dialog';import { ApiService } from 'src/app/services/api.service';@Component({  selector: 'app-dialog-wilayah',  templateUrl: './dialog-wilayah.component.html',  styleUrls: ['./dialog-wilayah.component.scss']})export class DialogWilayahComponent implements OnInit {  constructor(    private api: ApiService,    public dialogRef : MatDialogRef<DialogWilayahComponent>  ) { }  ngOnInit(): void {    this.getDataWilayah();  }  dataWilayah: any = [];  dataWilayahTemp: any = [];  getDataWilayah()  {    this.dataWilayah = [];    this.dataWilayahTemp = [];    this.api.get('org/wilayah').subscribe(res=>{      this.dataWilayah = res;      this.dataWilayahTemp = res;    })  }  initializeItems(): void {    this.dataWilayah = this.dataWilayahTemp;  }  searchTerm: string = '';  applyFilter(evt) {    this.initializeItems();    const searchTerm = evt.srcElement.value;    if (!searchTerm) {      return;    }    this.dataWilayah = this.dataWilayah.filter(wilayah => {      if (wilayah.nama && searchTerm) {        if (wilayah.nama.toLowerCase().indexOf(searchTerm.toLowerCase()) > -1) {          return true;        }        return false;      }    });  }  pilih(data)  {    this.dialogRef.close(data);  }}
```

### Dialog Hapus

#### dialog-hapus.component.html

```html
<h1 mat-dialog-title>Konfirmasi Hapus Data</h1><div mat-dialog-content>    <mat-grid-list cols="1" rows="1" rowHeight="6:1">      Masukkan kode dibawah ini untuk konfirmasi hapus data.      <mat-grid-tile>        <mat-card style="text-align: center; width: 40px; height: 20px;"><b>{{unixKarakter}}</b></mat-card>      </mat-grid-tile>    </mat-grid-list>        <div class="uk-flex uk-margin uk-flex-middle uk-text-center">      <div class="uk-width-expand" style="margin-right: 5px;">        <mat-form-field class="uk-width-1-1" appearance="outline">          <mat-label>Kode</mat-label>          <input [(ngModel)]="inputKonf" matInput>        </mat-form-field>      </div>      <div>        <button mat-button style="margin-top: -20px;" (click)="generateUnixChar()"><mat-icon style="color: mediumturquoise;">refresh</mat-icon></button>      </div>    </div>    <h5 style="color: red; margin-top: -30px;"><i>* Perhatikan besar kecil karakter</i></h5></div><div style="margin-top: -30px;" *ngIf="invalidCode == true" class="uk-alert alert-info uk-alert-danger">  Kode konfirmasi salah.</div><div mat-dialog-actions>  <span class="uk-width-expand"></span>  <button mat-button (click)="onNoClick()">Batal</button>  <button mat-raised-button color="warn" [disabled]="loading || inputKonf == undefined" (click)="konfirmasi()">{{loading ? 'Mohon tunggu...':'Hapus'}}</button></div>
```

#### dialog-hapus.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { DialogHapusComponent } from './dialog-hapus.component';describe('DialogHapusComponent', () => {  let component: DialogHapusComponent;  let fixture: ComponentFixture<DialogHapusComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ DialogHapusComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(DialogHapusComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

#### dialog-hapus.component.ts

```typescript
import { Component, Inject, OnInit } from '@angular/core';import { MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';import { ApiService } from 'src/app/services/api.service';@Component({  selector: 'app-dialog-hapus',  templateUrl: './dialog-hapus.component.html',  styleUrls: ['./dialog-hapus.component.scss']})export class DialogHapusComponent implements OnInit {  id:any;  from:any;  anggota_sel:any;  delMulti:boolean;  constructor(    public api:ApiService,    public dialogRef: MatDialogRef<DialogHapusComponent>,    @Inject(MAT_DIALOG_DATA) public sourceData: any  ) {    this.generateUnixChar();    this.from = sourceData.from;    if(sourceData.anggota_sel != undefined) {      this.anggota_sel = sourceData.anggota_sel;      this.delMulti = true;    } else {      this.id = sourceData.id;      this.delMulti = false;    }  }  ngOnInit(): void {  }  unixKarakter:any;  generateUnixChar() {    this.inputKonf = null;    this.unixKarakter = randomString(5, '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ');  }  inputKonf:any;  loading:boolean;  invalidCode:boolean;  konfirmasi() {    if(this.inputKonf == this.unixKarakter)    {      if(this.from == 'anggota' || this.from == 'anggota_sel') {        if(this.delMulti == false) {          this.api.delete('anggota/data_anggota/'+this.id).subscribe(res=>{            this.loading = false;            alert('Data anggota berhasil dihapus.');            this.dialogRef.close();          },(err) => {            this.loading = false;            alert('Gagal menghapus data.');          });        } else {          for(var i = 0; i<this.anggota_sel.length; i++) {            this.api.delete('anggota/data_anggota/'+this.anggota_sel[i].id).subscribe(res=>{            },(err) => {            });          }          this.loading = false;          alert('Data anggota berhasil dihapus.');          this.dialogRef.close();        }      } else if(this.from == 'users') {        this.api.delete('users/'+this.id).subscribe(res=>{          this.loading = false;          alert('Data '+this.from+' berhasil dihapus.');          this.dialogRef.close();        },(err) => {          this.loading = false;          alert('Gagal menghapus data.');        });      } else {        console.log(this.from)        this.api.delete('org/'+this.from+'/'+this.id).subscribe(res=>{          this.loading = false;          alert('Data '+this.from+' berhasil dihapus.');          this.dialogRef.close();        },(err) => {          this.loading = false;          alert('Gagal menghapus data.');        });      }    } else {      this.invalidCode = true;      var that = this;      setTimeout(function () {        that.invalidCode = undefined;      }, 1000);    }  }  onNoClick() {    this.dialogRef.close();  }}function randomString(length, chars) {  var result = '';  for (var i = length; i > 0; --i) result += chars[Math.floor(Math.random() * chars.length)];  return result;}
```

### Pengaturan

#### Organisasi

##### organisasi.component.html

```html
<h1 style="padding: 30px; margin: 0px">Pengaturan Kartu Anggota</h1><mat-card>  <div class="uk-margin-top">    <h2>Ketua Umum</h2>    <div      class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"      uk-grid    >      <mat-form-field class="uk-width-1-3" appearance="outline">        <mat-label>Nama</mat-label>        <input matInput [(ngModel)]="ktaData.nama_ketua"/>      </mat-form-field>      <mat-form-field class="uk-width-1-3" appearance="outline">        <mat-label>Nomor Anggota</mat-label>        <input matInput [(ngModel)]="ktaData.nba_ketua" />      </mat-form-field>      <div style="text-align: center" class="uk-width-1-3">        <p>          <b>Tanda Tangan</b>        </p>        <img src="{{photoBaseUrl+'config_images/'+ktaData.ttd_ketua}}" style="width: 100px" />        <div style="padding-top: 10px">          <button mat-button color="warn" (click)="ttdKetua()" [disabled]="loading['ttd_ketua']">{{loading['ttd_ketua'] ? 'Mengunggah..':'Ganti ttd'}}</button>        </div>      </div>    </div>    <h2>Sekretaris Umum</h2>    <div      class="uk-grid-column-small uk-grid-row-small uk-child-width-1-2@s"      uk-grid    >      <mat-form-field class="uk-width-1-3" appearance="outline">        <mat-label>Nama</mat-label>        <input matInput [(ngModel)]="ktaData.nama_sekretaris"/>      </mat-form-field>      <mat-form-field class="uk-width-1-3" appearance="outline">        <mat-label>Nomor Anggota</mat-label>        <input matInput [(ngModel)]="ktaData.nba_sekretaris"/>      </mat-form-field>      <div style="text-align: center" class="uk-width-1-3">        <p>          <b>Tanda Tangan</b>        </p>        <img src="{{photoBaseUrl+'config_images/'+ktaData.ttd_sekretaris}}" style="width: 100px" />        <div style="padding-top: 10px">          <button mat-button color="warn" (click)="ttdSekretaris()" [disabled]="loading['ttd_sekretaris']">{{loading['ttd_sekretaris'] ? 'Mengunggah..':'Ganti ttd'}}</button>        </div>      </div>    </div>    <h2>Stempel</h2>    <div style="text-align: left" class="uk-width-1-3">      <img src="{{photoBaseUrl+'config_images/'+ktaData.stampel}}" style="width: 100px" />      <div style="padding-top: 10px">        <button mat-button color="warn"(click)="stampel()" [disabled]="loading['stampel']">{{loading['stampel'] ? 'Mengunggah..':'Ganti stampel'}}</button>      </div>    </div>    <div class="uk-grid-small" uk-grid>      <div class="uk-width-expand"></div>      <div>        <button mat-flat-button color="primary" [disabled]="loadingSave" (click)="saveData()">{{ !loadingSave ? 'Simpan Perubahan':'Menyimpan..'}}</button>      </div>    </div>  </div>    <!-- Loading -->    <ngx-loading    [show]="loadingGet"    [config]="{      animationType: ngxLoadingAnimationTypes.chasingDots,      primaryColour: primaryColour,      secondaryColour: secondaryColour,      backdropBorderRadius: '3px'    }"    [template]="loadingTemplate"  ></ngx-loading> </mat-card>
```

##### organisasi.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { OrganisasiComponent } from './organisasi.component';describe('OrganisasiComponent', () => {  let component: OrganisasiComponent;  let fixture: ComponentFixture<OrganisasiComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ OrganisasiComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(OrganisasiComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

##### organisasi.component.ts

```typescript
import { Component, OnInit, TemplateRef, ViewChild } from '@angular/core';
import { MatDialog } from '@angular/material/dialog';
import { ImageUploaderComponent } from 'src/app/media/image-uploader/image-uploader.component';
import { ApiService } from 'src/app/services/api.service';
import { ngxLoadingAnimationTypes, NgxLoadingComponent } from 'ngx-loading';

const PrimaryWhite = '#ffffff';
const SecondaryGrey = '#ccc';
const PrimaryRed = '#dd0031';
const SecondaryBlue = '#1976d2';

@Component({
  selector: 'app-organisasi',
  templateUrl: './organisasi.component.html',
  styleUrls: ['./organisasi.component.scss']
})
export class OrganisasiComponent implements OnInit {

  displayedColumn: string[] = ['email', 'name', 'role', 'tgl_reg', 'status', '#'];
  ktaData: any = {};

  @ViewChild('ngxLoading', { static: false })
  ngxLoadingComponent!: NgxLoadingComponent;
  @ViewChild('customLoadingTemplate', { static: false })
  customLoadingTemplate!: TemplateRef<any>;
  @ViewChild('emptyLoadingTemplate', { static: false })
  emptyLoadingTemplate!: TemplateRef<any>;
  showingTemplate = false;
  public ngxLoadingAnimationTypes = ngxLoadingAnimationTypes;
  public primaryColour = PrimaryWhite;
  public secondaryColour = SecondaryGrey;
  public coloursEnabled = false;
  public loadingTemplate!: TemplateRef<any>;
  public config = {
    animationType: ngxLoadingAnimationTypes.none,
    primaryColour: this.primaryColour,
    secondaryColour: this.secondaryColour,
    tertiaryColour: this.primaryColour,
    backdropBorderRadius: '3px',
  };
  
  constructor(
    public api: ApiService,
    public dialog: MatDialog
  ) { }

  photoBaseUrl: any = this.api.photoBaseUrl;

  loadingGet:boolean;
  ngOnInit(): void {
    this.loadingGet = true;
    this.getData();
  }

  getData()
  {
    this.ktaData = {};
    this.api.get('config/kta').subscribe(res=>{
      this.ktaData = res;
      this.loadingGet = false;
    }, error => {
      this.loadingGet = false;
    })
  }

  loadingSave: boolean;
  saveData()
  {
    this.loadingSave = true;
    delete this.ktaData.stampel_dataurl;
    delete this.ktaData.ttd_sekretaris_dataurl;
    delete this.ktaData.ttd_ketua_dataurl;
    this.api.put('config/kta',this.ktaData).subscribe(res=>{
      this.loadingSave = false;
      alert('Perubahan berhasil disimpan.');
    },err=>{
      alert('Tidak dapat menyimpan perubahan.');
    })
  }

  loading: any = {};
  ttdKetua()
  {    
    let dialog = this.dialog.open(ImageUploaderComponent,{
      width: '500px'
    });
    dialog.afterClosed().subscribe(res=>{
      if(res)
      this.updateImage(res, 'ttd_ketua');
    });
  }
  ttdSekretaris()
  {    
    let dialog = this.dialog.open(ImageUploaderComponent,{
      width: '500px'
    });
    dialog.afterClosed().subscribe(res=>{
      if(res)
      this.updateImage(res, 'ttd_sekretaris');
    });
  }
  stampel()
  {    
    let dialog = this.dialog.open(ImageUploaderComponent,{
      width: '500px'
    });
    dialog.afterClosed().subscribe(res=>{
      if(res)
      this.updateImage(res, 'stampel');
    });
  }
  updateImage(image, attr)
  {
    this.loading[attr] = true;
    let name = new Date().getTime().toString();
    this.api.put('config/image',{image: image, attribute: attr, name:name }).subscribe(res=>{
      this.loading[attr] = false;
      this.getData();
      alert('Pembaruan gambar berhasil.');
    },err=>{
      this.loading[attr] = false;
      alert('Gagal mengunggah gambar.');
    })
  }


}

```

#### Pengguna

##### Dialog Pass Admin

###### dialog-pass-admin.component.html

```html
<h2 mat-dialog-title>Pembaruan Password</h2><div mat-dialog-content>    <mat-form-field class="uk-width-1-1">      <mat-label>Password Baru</mat-label>      <input matInput placeholder="your password" type="password" [(ngModel)]="user.p1">    </mat-form-field>    <mat-form-field class="uk-width-1-1">      <mat-label>Tulis Ulang Password Baru</mat-label>      <input type="password" matInput placeholder="Your password" [(ngModel)]="user.p2">    </mat-form-field></div><mat-dialog-actions align="end"><button mat-button mat-dialog-close>Batal</button><button mat-flat-button color="primary" [disabled]="loading || userData == undefined" (click)="reset()">{{loading ? 'TUNGGU..':'RESET'}}</button></mat-dialog-actions>
```

###### dialog-pass-admin.component.spec.ts

```typescript
import { async, ComponentFixture, TestBed } from '@angular/core/testing';import { DialogPassAdminComponent } from './dialog-pass-admin.component';describe('DialogPassAdminComponent', () => {  let component: DialogPassAdminComponent;  let fixture: ComponentFixture<DialogPassAdminComponent>;  beforeEach(async(() => {    TestBed.configureTestingModule({      declarations: [ DialogPassAdminComponent ]    })    .compileComponents();  }));  beforeEach(() => {    fixture = TestBed.createComponent(DialogPassAdminComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

###### dialog-pass-admin.component.ts

```typescript
import { Component, Inject, OnInit } from '@angular/core';
import { MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'app-dialog-pass-admin',
  templateUrl: './dialog-pass-admin.component.html',
  styleUrls: ['./dialog-pass-admin.component.css']
})
export class DialogPassAdminComponent implements OnInit {

  userData:any = {};
  constructor(
    private api: ApiService,
    public dialogRef : MatDialogRef<DialogPassAdminComponent>,
    @Inject(MAT_DIALOG_DATA) public dataSource:any,
  ) {
    this.userData = dataSource.data;
  }

  ngOnInit(): void {
  }

  user:any={p1:'',p2:''};

  loading:boolean;
  reset()
  {
    if(this.user.p1 != '' && this.user.p1 == this.user.p2)
    {
      this.loading=true;
      this.api.put('auth/reset',{password:this.user.p1, email:this.userData.email}).subscribe(res=>{
        alert('Pembaruan password berhasil');
        this.loading=false;
        this.dialogRef.close();
      },err=>{
        alert('Tidak dapat memperbarui password');
        this.loading=false;
      });
    }else{
      alert('Password tidak cocok');
    }
  }

  batal() {
    this.dialogRef.close();
  }

}

```

##### Dialog Ussers

######  dialog-users.component.html

```html
<h2 mat-dialog-title>Detail {{title}}</h2><div mat-dialog-content>  <mat-form-field class="uk-width-expand" appearance="outline">    <mat-label>Nama</mat-label>    <input [(ngModel)]="data.name" matInput placeholder="" required>  </mat-form-field>    <mat-form-field class="uk-width-expand" appearance="outline">    <mat-label>Email</mat-label>    <input [(ngModel)]="data.email" matInput placeholder="" required>  </mat-form-field>  <mat-form-field class="uk-width-expand" appearance="outline" >    <mat-label>Hak Akses Pengguna</mat-label>    <mat-select [(ngModel)]="data.role_id">      <mat-option *ngFor="let n of rules" value="{{n.id}}">{{n.label}}</mat-option>         </mat-select>  </mat-form-field>  <div *ngIf="isCreated == true">    <mat-form-field class="uk-width-expand" appearance="outline">      <mat-label>Password Baru</mat-label>      <input matInput placeholder="your password" type="password" required [(ngModel)]="data.password">    </mat-form-field>    <mat-form-field class="uk-width-expand" appearance="outline">      <mat-label>Tulis Ulang Password Baru</mat-label>      <input type="password" matInput placeholder="Your password" required [(ngModel)]="p2">    </mat-form-field>  </div></div><mat-dialog-actions align="end">  <button mat-button mat-dialog-close>Batal</button>  <button mat-flat-button color="primary" [disabled]="isloading" (click)="simpan()">{{isloading ? 'Mohon Tunggu...':'Simpan'}}</button></mat-dialog-actions>
```

###### dialog-users.component.spec.ts

```typescript
import { async, ComponentFixture, TestBed } from '@angular/core/testing';import { DialogUsersComponent } from './dialog-users.component';describe('DialogUsersComponent', () => {  let component: DialogUsersComponent;  let fixture: ComponentFixture<DialogUsersComponent>;  beforeEach(async(() => {    TestBed.configureTestingModule({      declarations: [ DialogUsersComponent ]    })    .compileComponents();  }));  beforeEach(() => {    fixture = TestBed.createComponent(DialogUsersComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

###### dialog-users.component.ts

```typescript
import { Component, Inject, OnInit } from '@angular/core';
import { MatDialogRef, MAT_DIALOG_DATA } from '@angular/material/dialog';
import { Router } from '@angular/router';
import { ApiService } from 'src/app/services/api.service';

@Component({
  selector: 'app-dialog-users',
  templateUrl: './dialog-users.component.html',
  styleUrls: ['./dialog-users.component.css']
})
export class DialogUsersComponent implements OnInit {

  data:any={};
  title = '';
  isCreated:boolean;
  constructor(
    private router: Router,
    private api: ApiService,
    public dialogRef : MatDialogRef<DialogUsersComponent>,
    @Inject(MAT_DIALOG_DATA) public dataSource:any,
  ) {
    this.title = 'Pengguna';
    this.checkRole();
    if(dataSource.data != undefined) {   
      this.isCreated = false; 
      this.data = dataSource.data;
      if(this.data.role_id == 'admin_wilayah')
      {
        this.getWilayah();
      }
      if(this.data.role_id == 'admin_daerah')
      {
        this.getDaerah();
      }

    } else {
      this.data = {};
      this.isCreated = true;
    }
  }

  ngOnInit(): void {
  }

  userData:any = {};
  checkRole()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res;    
      if(this.userData.role_id == 'superadmin') {
        this.rules.push({
          id    : 'superadmin',
          label : 'Superadmin'
        },
        {
          id    : 'eksternal',
          label : 'Eksternal'
        },
        {
          id    : 'admin_wilayah',
          label : 'Admin Wilayah'
        },
        {
          id    : 'admin_daerah',
          label : 'Admin Daerah'
        });
      } else if(this.userData.role_id == 'admin_wilayah') {
        this.rules.push(
        {
          id    : 'admin_daerah',
          label : 'Admin Daerah'
        });
      }
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  cekData() {
    this.dataWilayah = [];
    this.dataDaerah = [];
    if(this.data.role_id == 'admin_wilayah')
    {
      this.getWilayah();
    }
    if(this.data.role_id == 'admin_daerah')
    {
      this.getDaerah();
    }
  }

  dataWilayah: any = [];
  getWilayah()
  {
    this.api.get('org/wilayah').subscribe(res=>{
      this.dataWilayah = res;
    })
  }

  dataDaerah: any = [];
  getDaerah()
  {
    this.api.get('org/daerah').subscribe(res=>{
      this.dataDaerah = res;
    })
  }

  rules: any = [];

  isloading:boolean;
  p2:any;
  simpan() {
    this.isloading = true;
    var conf = confirm('Yakin ingin menyimpan data?');
    if (conf) {
      if(this.isCreated == true) {
        if(this.data.password != '' && this.data.password == this.p2)
        {
          this.data.is_active = 1;
          this.data.date_created = new Date();
          // this.data.id = new Date().getTime().toString() + '' + [Math.floor((Math.random() * 1000))];
          this.api.post('auth/user/',this.data).subscribe(res=>{
            alert('Data berhasil disimpan.');
            this.isloading=false;
            this.dialogRef.close();
          },err=>{
            this.isloading=false;
            alert('Ada masalah. Coba lagi!');
          })
        }else{
          alert('Password tidak cocok');
        }
      } else {
        this.api.put('auth/user/'+this.data.id,this.data).subscribe(res=>{
          alert('Data berhasil diperbarui.');
          this.isloading=false;
          this.dialogRef.close();
        },err=>{
          this.isloading=false;
          alert('Ada masalah. Coba lagi!');
        })
      }
    }
  }

  batal() {
    this.dialogRef.close();
  }

}

```

##### Pengguna

###### pengguna.component.html

```html
<h1 style="padding: 30px; margin: 0px;">Pengelolaan Pengguna</h1>    <mat-card>        <div class="uk-grid-small" uk-grid>            <mat-form-field class="uk-width-1-4" appearance="outline">            <mat-label>Cari Pengguna ...</mat-label>            <input matInput (keydown)="applyFilter($event.target.value)" type="search">            </mat-form-field>            <div class="uk-width-expand"></div>            <div>                <button mat-flat-button color="primary" (click)="detailUsers(null)">                    Tambah Pengguna                </button>            </div>        </div>        <table mat-table matSort (matSortChange)="sortData($event)" [dataSource]="dataSource" class="anggota-table">                       <ng-container matColumnDef="email">                <th mat-sort-header="email" mat-header-cell *matHeaderCellDef> Email </th>                <td mat-cell *matCellDef="let element"> {{element.email}} </td>            </ng-container>            <ng-container matColumnDef="name">                <th mat-sort-header="name" mat-header-cell *matHeaderCellDef> Nama </th>                <td mat-cell *matCellDef="let element"> {{element.name}} </td>            </ng-container>            <ng-container matColumnDef="role">                <th mat-sort-header="role" mat-header-cell *matHeaderCellDef> Role </th>                <td mat-cell *matCellDef="let element"> {{element.role_id}} <br>                     <div *ngIf="element.role_id == 'admin_wilayah'">                        ( {{dataWilayah[element.kode_area] == undefined ? 'Tentukan Wilayah!':dataWilayah[element.kode_area].nama}} )                    </div>                    <div *ngIf="element.role_id == 'admin_daerah'">                        ( {{dataDaerah[element.kode_area] == undefined ? 'Tentukan Daerah!':dataDaerah[element.kode_area].nama}} )                    </div>                </td>            </ng-container>            <ng-container matColumnDef="tgl_reg">                <th mat-sort-header="created_at" mat-header-cell *matHeaderCellDef> Tgl Registrasi </th>                <td mat-cell *matCellDef="let element"> {{element.date_created | date:'dd MMM yyyy'}} </td>            </ng-container>            <ng-container matColumnDef="status">                <th mat-sort-header="status" mat-header-cell *matHeaderCellDef> Status </th>                <td mat-cell *matCellDef="let element"> {{element.is_active == 0 ? 'Nonaktif':'Aktif'}} </td>            </ng-container>            <ng-container matColumnDef="#">                <th mat-header-cell *matHeaderCellDef style="text-align: right;">#</th>                <td mat-cell *matCellDef="let element" style="text-align: right;">                    <button mat-icon-button [matMenuTriggerFor]="menu1">                        <mat-icon>more_vert</mat-icon>                    </button>                    <mat-menu #menu1="matMenu">                        <button (click)="aktivasi(element)" [disabled]="element.email == userData.email" mat-menu-item>{{element.is_active == 1 ? 'Nonaktifkan':'Aktifkan'}}</button>                                                <button mat-menu-item (click)="detailUsers(element)">Detail</button>                        <button mat-menu-item *ngIf="element.role_id == 'admin_wilayah'" (click)="pilihWilayah(element)">Tentukan Wilayah</button>                        <button mat-menu-item *ngIf="element.role_id == 'admin_daerah'" (click)="pilihDaerah(element)">Tentukan Daerah</button>                        <button *ngIf="userData.role_id == 'superadmin'" mat-menu-item (click)="updatePassword(element)">Ubah Password</button>                        <button *ngIf="userData.role_id == 'superadmin'" [disabled]="element.email == userData.email" mat-menu-item (click)="deleteUser(element.id)">Hapus Pengguna</button>                    </mat-menu>                </td>            </ng-container>            <tr mat-header-row *matHeaderRowDef="displayedColumn; sticky: true"></tr>            <tr mat-row *matRowDef="let row; columns: displayedColumn;"></tr>        </table>        <mat-spinner *ngIf="showSpinner" style="margin:0 auto; size: 10px;margin-top: 10px;" mode="indeterminate" [diameter]="60"></mat-spinner>        <mat-paginator [pageSizeOptions]="[10, 100, 500]" showFirstLastButtons></mat-paginator>    </mat-card>
```

###### pengguna.component.spec.ts

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';import { PenggunaComponent } from './pengguna.component';describe('PenggunaComponent', () => {  let component: PenggunaComponent;  let fixture: ComponentFixture<PenggunaComponent>;  beforeEach(async () => {    await TestBed.configureTestingModule({      declarations: [ PenggunaComponent ]    })    .compileComponents();  });  beforeEach(() => {    fixture = TestBed.createComponent(PenggunaComponent);    component = fixture.componentInstance;    fixture.detectChanges();  });  it('should create', () => {    expect(component).toBeTruthy();  });});
```

###### pengguna.component.ts

```typescript
import { Component, OnInit, ViewChild } from '@angular/core';
import { MatDialog } from '@angular/material/dialog';
import { MatPaginator } from '@angular/material/paginator';
import { Sort } from '@angular/material/sort';
import { MatTableDataSource } from '@angular/material/table';
import { Router } from '@angular/router';
import { ApiService } from 'src/app/services/api.service';
import { DialogDaerah } from '../../../database/data-daerah/data-daerah.component';
import { DialogDaerahComponent } from '../../../database/dialog-daerah/dialog-daerah.component';
import { DialogPassAdminComponent } from '../dialog-pass-admin/dialog-pass-admin.component';
import { DialogUsersComponent } from '../dialog-users/dialog-users.component';
import { DialogWilayahComponent } from '../../../database/dialog-wilayah/dialog-wilayah.component';
import { DialogHapusComponent } from 'src/app/public/dialog-hapus/dialog-hapus.component';

@Component({
  selector: 'app-pengguna',
  templateUrl: './pengguna.component.html',
  styleUrls: ['./pengguna.component.scss']
})
export class PenggunaComponent implements OnInit {

  displayedColumn: string[] = ['email', 'name', 'role', 'tgl_reg', 'status', '#'];
  dataUsers:any=[];
  sortedData:any=[];
  dataSource = new MatTableDataSource(this.sortedData);
  @ViewChild(MatPaginator) paginator: MatPaginator;
  constructor(
    private router: Router,
    private dialog: MatDialog,
    public api:ApiService
  ) { }

  ngOnInit(): void {
    this.checkRole();
  }

  userData:any = {};
  checkRole()
  {
    this.api.get('users/me').subscribe(res=>{
      this.userData=res;     
      this.getUsers();   
      this.getWilayah(); 
      this.getDaerah();
    },err=>{
      this.router.navigate(['auth/login']);
    })
  }

  showSpinner: boolean = true;
  getUsers()
  {
    this.dataUsers = [];
    this.api.get('users').subscribe(res=>{
      this.dataUsers=res;
      this.sortedData = this.dataUsers.slice();
      this.dataSource = new MatTableDataSource(this.sortedData);
      this.dataSource.paginator = this.paginator;
      this.showSpinner = false;
    })
  }

  dataWilayah:any = {};
  dataDaerah:any = {};
  getWilayah() {
    this.api.get('org/wilayah').subscribe(res=>{
      this.parseWilayah(res);
    })
  }

  parseWilayah(data) {
    for(var i=0; i<data.length; i++)
    {
      this.dataWilayah[data[i].id_wilayah] = data[i];
    }
  }

  getDaerah() {
    this.api.get('org/daerah').subscribe(res=>{
      this.parseDaerah(res);
    })
  }

  parseDaerah(data) {
    for(var i=0; i<data.length; i++)
    {
      this.dataDaerah[data[i].id_daerah] = data[i];
    }
  }

  applyFilter(filterValue: string) {
    filterValue = filterValue.trim(); // Remove whitespace
    filterValue = filterValue.toLowerCase(); // MatTableDataSource defaults to lowercase matches
    this.dataSource.filter = filterValue;
  }

  sortData(sort: Sort) {
    const data = this.dataUsers.slice();
    if (!sort.active || sort.direction === '') {
      this.sortedData = data;
      return;
    }

    this.sortedData = data.sort((a, b) => {
      const isAsc = sort.direction === 'asc';
      switch (sort.active) {
        case 'id': return compare(a.id, b.id, isAsc);
        case 'email': return compare(a.email, b.email, isAsc);
        case 'name': return compare(a.name, b.name, isAsc);
        case 'role': return compare(a.role, b.role, isAsc);
        case 'tgl_reg': return compare(a.tgl_reg, b.tgl_reg, isAsc);
        case 'status': return compare(a.status, b.status, isAsc);
        default: return 0;
      }
    });
    this.dataSource = new MatTableDataSource(this.sortedData);
    this.dataSource.paginator = this.paginator;
  }

  detailUsers(data) {
    const dialogRef = this.dialog.open(DialogUsersComponent, {
      width: '500px',
      data: {
        data: data
      }
    });

    dialogRef.afterClosed().subscribe(result => {
      this.checkRole();
    });
  }

  pilihDaerah(data)
  {
    const dialogRef = this.dialog.open(DialogDaerahComponent, {
      width: '500px',
      data: data
    });
    dialogRef.afterClosed().subscribe(result => {
      if(result)
      {
        this.updateKodeArea(data.id, result.id_daerah);
      }
    });
  }

  pilihWilayah(data)
  {
    const dialogRef = this.dialog.open(DialogWilayahComponent, {
      width: '500px',
      data: data
    });
    dialogRef.afterClosed().subscribe(result => {
      if(result)
      {
        this.updateKodeArea(data.id, result.id_wilayah);
      }
    });
  }

  updateKodeArea(id_user, kode)
  {
    this.api.put('users/kodearea/'+id_user,{kode_area:kode}).subscribe(res=>{
      alert('Kode area berhasil ditentukan untuk pengguna yang anda pilih');
      this.getUsers();
    },err=>{
      alert('Ada masalah, coba lagi.');
    });
  }

  updatePassword(data) {
    const dialogRef = this.dialog.open(DialogPassAdminComponent, {
      data: {
        data: data
      }
    });

    dialogRef.afterClosed().subscribe(result => {
      this.checkRole();
    });
  }

  //aktivasi
  isloading: any = {};
  aktivasi(dat) {
    this.isloading = true;
    var dt = {
      is_active: dat.is_active == 1 ? 0 : 1
    }
    this.api.put('auth/user/'+dat.id, dt).subscribe(res=>{
      alert('Data berhasil disimpan.');
      this.isloading=false;
      this.checkRole();
    },err=>{
      this.isloading=false;
      alert('Ada masalah. Coba lagi!');
    })
  }

  gantiRole(dat) {
    this.isloading = true;
    var dt = {
      role_id: dat.role_id == 1 ? 2 : 1
    }
    this.api.put('auth/user/'+dat.id, dt).subscribe(res=>{
      alert('Data berhasil disimpan.');
      this.isloading=false;
      this.checkRole();
    },err=>{
      this.isloading=false;
      alert('Ada masalah. Coba lagi!');
    })
  }

  transform(str) {
    var splitStr = str.toLowerCase().split(' ');
    for (var i = 0; i < splitStr.length; i++) {
        splitStr[i] = splitStr[i].charAt(0).toUpperCase() + splitStr[i].substring(1);     
    }
    return splitStr.join(' '); 
  }

  loading:boolean;
  deleteUser(id) {
    const dialogRef = this.dialog.open(DialogHapusComponent, {
      width: '450px',
      data: {id:id, from: 'users'}
    });
    dialogRef.afterClosed().subscribe(result => {
      this.loading = false;
      if(result != undefined) {
        alert('Data Pengguna berhasil dihapus.');
        this.checkRole();
      }
    },(err) => {
      this.loading = false;
      alert('Gagal menghapus data.');
    });
  }

}

function compare(a: number | string, b: number | string, isAsc: boolean) {
  return (a < b ? -1 : 1) * (isAsc ? 1 : -1);
}
```

## Services

### api.service.spec.ts

```typescript
import { TestBed } from '@angular/core/testing';

import { ApiService } from './api.service';

describe('ApiService', () => {
  let service: ApiService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(ApiService);
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });
});
```

### api.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Observable } from 'rxjs/internal/Observable';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  serverUrl='https://api.sunhouse.co.id/ppa/index.php/';
  public fileBaseUrl='https://api.sunhouse.co.id/ppa/';
  public photoBaseUrl='https://api.sunhouse.co.id/ppa/photos/';
  httpOption:any;
  constructor(
    public http:HttpClient
  ) 
  {
    
  }
  token:any;
  getToken()
  {
    var tokens=localStorage.getItem('ppaToken');
    this.token={email:'',jwt:''};
    if(tokens!=null)
    {
      this.token=JSON.parse(tokens);      
    }
    this.httpOption = {
      headers: new HttpHeaders({
        'Content-Type':  'application/json',
        'Authorization': 'Bearer '+this.token.jwt
      })
    };
  }
  generateOption(bearer)
  {
    this.httpOption = {
      headers: new HttpHeaders({
        'Content-Type':  'application/json',
        'Authorization': 'Bearer '+bearer
      })
    };
  }
  me()
  {
    this.getToken();
    return this.http.get(this.serverUrl+'users/me', this.httpOption);
  }  
  get(url)
  {
    this.getToken();
    return this.http.get(this.serverUrl+url, this.httpOption);
  }
  getWithBearer(url,bearer)
  {
    this.generateOption(bearer);
    return this.http.get(this.serverUrl+url, this.httpOption);
  }
  post(url,data)
  {
    this.getToken();
    return this.http.post(this.serverUrl+url,data,  this.httpOption);
  }
  postWithBearer(url,data,bearer)
  {
    this.generateOption(bearer);
    return this.http.post(this.serverUrl+url,data, this.httpOption);
  }
  put(url,data)
  {
    this.getToken();
    return this.http.put(this.serverUrl+url,data,  this.httpOption);
  }
  putWithBearer(url,data,bearer)
  {
    this.generateOption(bearer);
    return this.http.put(this.serverUrl+url,data, this.httpOption);
  }
  delete(url)
  {
    this.getToken();
    return this.http.delete(this.serverUrl+url,  this.httpOption);
  }
  import(file)
  {
    //this.getToken();
    return this.http.post(this.serverUrl+'import',file);
  }

  upload(url,data)
  {
    this.getToken();
    this.httpOption = {
      headers: new HttpHeaders({       
        'Authorization': 'Bearer '+this.token.jwt
      })
    };
    return this.http.post(this.serverUrl+url,data,  this.httpOption);
  }

  uploadImage(url,data) {
    this.getToken();
    this.httpOption = {
      headers: new HttpHeaders({       
        'Authorization': 'Bearer '+this.token.jwt
      })
    };

    return this.http.post(this.serverUrl+url,data,  this.httpOption);
  }

  uploadDoc(url,data) {
    this.getToken();
    this.httpOption = {
      headers: new HttpHeaders({       
        'Authorization': 'Bearer '+this.token.jwt
      })
    };

    return this.http.post(this.serverUrl+url,data,  this.httpOption);
  }

  user()
  {
    var tokens=localStorage.getItem('kapeToken');
    this.token={email:'',jwt:'',username:'',tahunPelajaran:'',semester:''};
    if(tokens!=null)
    {
      this.token=JSON.parse(tokens);      
    }  
    return this.token; 
  }


}

```

