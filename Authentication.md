# Backend API

## API with Authentication

API (JSON Server) wrapped in Node.js code now requires authentication.

## Install

```bash
cd labs\solutions\project-manage
git checkout auth
npm install
```

> The npm install only adds the `jsonwebtoken` node package (library) which is used to create and parse JSON Web Tockens in the Node.js custom server

## Run

```bash
npm run api:auth
```

## SignIn API

```
POST http://localhost:3000/auth/signin
```

### Request Content-Type

```
Content-Type: application/json
```

### Request Body

```
{
  "email": "craig@test.com",
  "password":"abc123"
}
```

- See `api\user-credentials.json` for additional valid email/password combinations or to change them.
  > Note: if you change data in `api\user-credentials.json` the server will need to be restarted before you will see the changes.

### Response Body (200 OK)

```
{
   "access_token": "XXXXXXXXXXXXXXXX"
}
```

## Authentication Required

Authentication is now required API for any json-server API calls including:

```
GET     http://localhost:3000/projects
GET     http://localhost:3000/projects/1
POST    http://localhost:3000/projects
PUT     http://localhost:3000/projects/1
DELETE  http://localhost:3000/projects/1
```

## Auth Request Header (required access token)

```
Authorization: Bearer XXXXXX
```

### Response Body (401 Unauthorized)

If access token is is expired or invalid (tampered with) the response will be:

```
{
  "status": 401,
  "message": "Error access token is invalid or has expired."
}
```

### Testing

To test run the requests in the files inside the `test` directory using a tool like [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) in Visual Studio Code.

- `singin-post.http`
- `projects-get-auth.http`
  - The token that appears in the header after `Authorization: Bearer` will need to be updated with the `access_token` from the response received from `signin-post.http`.
  - Leave the `Bearer` before supplying the token (XXXXX)
    `Authorization: Bearer XXXXXX`
    These are the same requests a client like an `Angular` application makes.

### Resources

[JSON Web Tokens (Basics/JWT)](https://medium.com/@piraveenaparalogarajah/json-web-tokens-jwt-basics-6515b13077e8)

[CORS Documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

[Uses OAuth 2](https://security.stackexchange.com/questions/108662/why-is-bearer-required-before-the-token-in-authorization-header-in-a-http-re)

[Preflight requests with CORS](http://restlet.com/company/blog/2015/12/15/understanding-and-using-cors/)

[Forked from](https://github.com/oz4you/mock-auth-json-server)

# Frontend Angular Application

```
ng g module account --routing --module app
ng g s account/shared/user
ng g c account/signin
ng g c account/account-header
ng g s account/shared/securityguard
npm i @auth0/angular-jwt
```

#### `src\account\shared\UserService.ts`

```ts
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';
import { environment } from 'src/environments/environment';
import { JwtHelperService } from '@auth0/angular-jwt';

@Injectable({
  providedIn: 'root',
})
export class UserService {
  get isSignedIn() {
    return !!localStorage.getItem('auth_token');
  }

  static get authorizationToken() {
    return localStorage.getItem('auth_token') || undefined;
  }

  get authenticatedUser() {
    let payload = new JwtHelperService().decodeToken(
      UserService.authorizationToken
    );
    return payload;
  }

  constructor(
    private http: HttpClient // private jwtHelper: JwtHelperService
  ) {}

  signin(email: string, password: string) {
    return this.http
      .post(environment.backendUrl + '/auth/signin', { email, password })
      .pipe(
        map((res: any) => {
          if (res.success) {
            localStorage.setItem('auth_token', res.access_token);
          }
          return res.success;
        }),
        catchError((error: HttpErrorResponse) => {
          console.error(error);
          return throwError(
            'The email or password you have entered is invalid.'
          );
        })
      );
  }

  signout() {
    localStorage.removeItem('auth_token');
  }
}
```

#### `src\account\shared\SecurityGuard.ts`

```ts
import { Injectable } from '@angular/core';
import {
  ActivatedRouteSnapshot,
  CanActivate,
  Router,
  RouterStateSnapshot,
  UrlTree,
} from '@angular/router';
import { Observable } from 'rxjs';
import { UserService } from './user.service';

@Injectable({
  providedIn: 'root',
})
export class SecurityGuard implements CanActivate {
  constructor(private userService: UserService, private router: Router) {}
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ):
    | boolean
    | UrlTree
    | Observable<boolean | UrlTree>
    | Promise<boolean | UrlTree> {
    if (this.userService.isSignedIn) {
      return true;
    } else {
      this.router.navigate(['/signin']);
      return false;
    }
  }
}
```

#### `src\app\core\auth.interceptor.ts`

```ts
import {
  HttpHandler,
  HttpInterceptor,
  HttpRequest,
} from '@angular/common/http';
import { Injectable } from '@angular/core';
import { UserService } from '../account/shared/user.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor() {}

  intercept(req: HttpRequest<any>, next: HttpHandler) {
    // Get the auth token
    const authToken = UserService.authorizationToken ?? '';

    // Clone the request and replace the original headers with
    // cloned headers, updated with the authorization.
    const authReq = req.clone({
      headers: req.headers.set('Authorization', 'Bearer ' + authToken),
    });

    // send cloned request with header to the next handler.
    return next.handle(authReq);
  }
}
```

#### `src\app\app.module.ts`

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ProjectsModule } from './projects/projects.module';
import { HomeModule } from './home/home.module';
import { AccountModule } from './account/account.module';
import { JwtModule } from '@auth0/angular-jwt';
import { AuthInterceptor } from './core/auth.interceptor';
import { UserService } from './account/shared/user.service';

export function tokenGetter() {
  return UserService.authorizationToken ?? '';
}

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ProjectsModule,
    HttpClientModule,
    HomeModule,
    AccountModule,
    JwtModule.forRoot({
      config: {
        tokenGetter: tokenGetter,
        allowedDomains: ['localhost:4200', 'localhost:3000'],
        disallowedRoutes: [
          // '//localhost:4200/home/',
          // '//localhost:4200/signin/',
        ],
      },
    }),
  ],
  // providers: [
  //   { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true },
  // ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

#### `src\app\account\signin`

```ts
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from '@angular/forms';
import { Router } from '@angular/router';
import { UserService } from '../shared/user.service';

@Component({
  selector: 'app-signin',
  templateUrl: './signin.component.html',
  styleUrls: ['./signin.component.css'],
})
export class SigninComponent implements OnInit {
  signinForm!: FormGroup;
  message: string = '';

  constructor(
    private userService: UserService,
    private router: Router,
    private formBuilder: FormBuilder
  ) {}

  ngOnInit() {
    this.signinForm = this.formBuilder.group({
      email: [],
      password: [],
    });
  }

  onSubmit() {
    const formValues = this.signinForm?.value;
    console.log(formValues);
    this.userService.signin(formValues.email, formValues.password).subscribe(
      (result) => {
        if (result) {
          this.router.navigate(['/projects']);
        }
      },
      (error) => {
        this.message = error;
      }
    );
  }
}
```
