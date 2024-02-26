# Diseño de Arquitectura (Propuesta)

## Frontend

Es bien sabido que estos conceptos de arquitectura por lo general siempre se aplican para proyectos basados en tecnologias Backend, pero que hay sobre el Frontend ? acaso no es posible realizar buenas implementaciones de arquitectura sobre frameworks como Angular ? bueno veamoslo ...

## Propuesta

### Conceptos Principales

- Programación Orientada a Objetos
- Principios de Diseño
- Patrones de Diseño

Estos conceptos, paradigmas y patrones son fundamentales en el entendimiento general para una buena arquitectura de software, pero en si que es Arquitectura de Software.

 > Una arquitectura de [software], también denominada arquitectura [lógica], consiste en un conjunto de patrones y abstracciones coherentes que proporcionan un marco definido y claro para interactuar con el código fuente del software. - Wikipedia

 Esta es una definición generica que podemos encontrar en muchos libros y articulos, pero la arquitectura en lo personal y en el ambito del desarrollo de software es **El mayor valor**

 ## Entendamos la Arquitectura

 En el desarrollo de software nos encontramos con toda clase de retos y diferentes soluciones a esos retos, cada pieza de software que se construye se construye pensando en que funcione y que genere ganancias rapidamente. Pero es ese el camino ? correr como liebres apresuradamente escribiendo código lo más rapido posible, bueno prefiero ser la tortuga, la liebre es arrogante y lo va a lamentar.

 ## Sentando las bases

 No podemos hablar de arquitectura sin conocer los fundamentos sobre los que se construyen. se deben entender y aplicar en cada linea de código que se defina.

 ## Hagamos una App Todo!

 Debemos entender la arquitectura desde lo más basico e ir escalando hacia lo más avanzado, como la aplico en una simple aplicación y luego llevar esto al siguiente nivel.

## Alto Nivel

```typescript
// Entitie
interface TodoData {
    task: string
    date: Date
    priority: priority
}

// Type
type priority = "high" | "medium" | "low";

// Design
interface Crud<T, K> {
    create(payload: T): boolean
    update(payload: T): boolean
    remove(id: number): boolean
}
```

## Caso de Uso
```typescript
interface TodoUseCase extends Crud<TodoData, any> {
    readById(id: number): Array<TodoData>;
}
```

## Detalles de Implementación

```typescript
class Todo implements TodoUseCase {

    create(payload: TodoData): boolean {
        return true
    }

    update(payload: TodoData): boolean {
        return true
    }

    readById(id: number): TodoData[] {
        return [];
    }

    remove(id: number): boolean {
        return true;
    }
}
```

## UI
```typescript
// Model
abstract class ViewOutputLogic {
    form: any
    isLoading: boolean = false;
}

interface ViewInputLogic {
    setView(view: any): void
    createTodoAction(): void
    success(response: boolean): boolean
}

interface ViewInteractorLogic {
    setPresenter(presenter: any): void
    processAction(): boolean
}

// Interactor
class ComponentInteractor implements ViewInteractorLogic {
    private _todoUseCase!: TodoMySQLUseCase;
    private _presenter!: ViewInputLogic;

    constructor(todoUseCase: TodoMySQLUseCase) {
        this._todoUseCase = todoUseCase;
    }

    setPresenter(presenter: ViewInputLogic): void {
        this._presenter = presenter;
    }

    processAction(): boolean {
        let task: TodoData = {
            task: "Do my homework",
            date: new Date(),
            priority: "low"
        }

        return this._presenter.success(this._todoUseCase.create(task));
    }
}

// Presenter
class ComponentPresenter implements ViewInputLogic {
    private _view!: ViewOutputLogic;
    private _interactor!: ViewInteractorLogic

    constructor() {
        this._interactor.setPresenter(this);
    }

    setView(view: ViewOutputLogic): void {
        this._view = view;
    }

    createTodoAction(): void {
        this._interactor.processAction();
    }

    success(response: boolean): boolean {
        this._view.isLoading = response;
        return response;
    }
}

// View
class ComponentView extends ViewOutputLogic {
    private _presenter!: ViewInputLogic

    constructor() {
        super();
        this._presenter.setView(this);
    }

    titleApp(): void {

    }

    showMenu(): void {

    }

    chooseOptionMenu(id: string): void {

    }

    createTodoAction(): void {
        this._presenter.createTodoAction();
    }
}
```

## Bajo Nivel
```typescript
// Module
class Application {
    private _view!: ComponentView;

    constructor(view: ComponentView) {
        this._view = view;
    }

    start(): void {
        this._view.titleApp();
    }
}

//concreción - low level system
const app = new Application(
    new ComponentView()
);
```

Se busca con esta propuesta mejorar la escalablidad y mantenibilidad del proyecto usando el patron Clean Architecture y en la capa UI MVVM.

## Patron a seguir
![Clean Architecture](./clean-architecture.png)

## Esquema de Directorio

### Domain
<sub>Reglas de negocio</sub>

La politica de mas alto nivel compuesto, por Entidades, Interfaces y Casos de uso si es necesario.

### Application
<sub>Gstion de datos</sub>

Donde realizaremos la gestion de la data a consumir basandonos en los detalles de especificacion de cada servicio

### UI
<sub>Presentacion</sub>

Definicion de las vistas la capa mas alejada del negocio, la composicion de cada vista estara definida de la siguiente formato:

- Interactor
    - Capa que gestiona el negocio donde se inyectan las dependencias necesarias.
- Presenter
    - Capa que realiza la transformacion de los datos de acuerdo a lo entregado por el interactor para ser luego entregado a la vista.
- View
    - Presentacion de los datos, captura de eventos por parte del usuario control de flujo inicial.

# SOLID

Los buenos sistemas de software comienzan con un buen codigo. Los principios SOLID nos ayudaran a ordenar nuestras estructuras de datos y como estas deben interconectarse.

# Ejemplo Directorio

* app
    * analytics
    * application
        * services
        * usecases
        * providers
    * network
        * requests
        * responses
    * domain
        * entities
        * interfaces
    * common
        * utils
        * helpers
        * errors
    * core
        * directives
        * guards
        * constants
        * forms
        * pipes
        * validators
    * shared
        * UIComponents
    * ui
        * auth
            * interactor
            * presenter
            * view
                * model
                    * auth.model.ts
              auth.component.html
              auth.component.ts
        * auth.module.ts
        * auth.routing.ts

#

## Ejemplo de Codigo

Ejemplo de Vista
```typescript
import { Component, Inject, OnDestroy, OnInit } from "@angular/core";
import { Router } from "@angular/router";
import { contextMenuOptions } from "@core/constants/options.enum";
import { Products } from "@domain/entities/products.entitie";
import { FunctionOneInputLogic, FunctionOneOutputLogic } from "./model/functionone.model";

@Component({
    selector: 'function-one-view',
    templateUrl: './functionone.component.html'
})
export class FunctionOneComponent extends FunctionOneOutputLogic implements OnInit, OnDestroy {

    constructor(
        @Inject('functionOnePresenterProvider') private _presenter: FunctionOneInputLogic,
        private _router: Router
    ) {
        super();
        this._presenter.setView(this);
    }

    ngOnInit(): void {
        this._presenter.getProducts();
    }

    makeFilterWithinCriteriaValue(value: string): void {
        this._presenter.makeFilterWithinCriteriaValue(value);
    }

    redirectTo(): void {
        this._router.navigate(['function-two']);
    }

    openContextMenu(id: string): void {
        this._presenter.showContextMenu(id);
    }

    contextMenuOption(event: string, product: Products): void {
        (event === contextMenuOptions.editAction)
            ? this._router.navigate(['function-two'], { queryParams: product })
            : this._presenter.showModalConfirmation(product.name, product.id)
    }

    processDeleteProduct(event: boolean): void {
        if (event)
            this._presenter.processDelete();
    }

    ngOnDestroy(): void {
        this._presenter.unsubscribe();
    }
}
```

Model
```typescript

import { HttpErrorResponse, HttpResponse } from "@angular/common/http";
import { productsTitleColumns } from "@core/constants/products.columns";
import { CoreInteractor } from "@core/view/core.interactor";
import { CorePresenter } from "@core/view/core.presenter";
import { Products } from "@domain/entities/products.entitie";

export abstract class FunctionOneOutputLogic {
    products: Products[] | null = [];
    productsColumns = productsTitleColumns;
    showMenu: boolean | undefined = false;
    selectedContextMenu!: string;
    isOpenModal: boolean = false;
    selectedProductName!: string;
    productId!: string;
}

export interface FunctionOneInputLogic extends CorePresenter {
    getProducts(): void
    processResponse(response: HttpResponse<Array<Products>>): void
    errorRequest(error: HttpErrorResponse): void
    makeFilterWithinCriteriaValue(value: string): void
    showContextMenu(id: string): void
    showModalConfirmation(productName: string, id: string): void
    processDeleteConfirmation(response: HttpResponse<any>): void
    processDelete(): void
    unsubscribe(): void
}

export interface FunctionOneInteractorLogic extends CoreInteractor {
    processProductRequest(): void
    processProductDelete(id: string): void
    unsubscribe(): void
}

```

Ejemplo de Presenter
```typescript
import { HttpErrorResponse, HttpResponse, HttpStatusCode } from "@angular/common/http";
import { Inject, Injectable } from "@angular/core";
import { UtilsServices } from "@common/utils/utils.service";
import { Products } from "@domain/entities/products.entitie";
import { FunctionOneInputLogic, FunctionOneInteractorLogic, FunctionOneOutputLogic } from "../view/model/functionone.model";
import { Messages } from "@core/constants/messages.enum";

@Injectable()
export class FunctionOnePresenter implements FunctionOneInputLogic {
    private _view!: FunctionOneOutputLogic;

    constructor(
        @Inject('functionOneInteractorProvider') private _interactor: FunctionOneInteractorLogic
    ) {
        this._interactor.setPresenter(this);
    }

    setView(component: FunctionOneOutputLogic): void {
        this._view = component;
    }

    makeFilterWithinCriteriaValue(value: string): void {
        (UtilsServices.isEmptyOrNull(value))
            ? this.getProducts()
            : this._view.products = this.filterResult(value);
    }

    getProducts(): void {
        this._interactor.processProductRequest();
    }

    private filterResult(value: string): Products[] {
        return this._view.products?.filter(product => product.name.toLocaleLowerCase().includes(value)) as Products[];
    }

    processResponse(response: HttpResponse<Array<Products>>): void {
        this._view.products = response.body;
    }

    errorRequest(error: HttpErrorResponse): void {
        (error.status === HttpStatusCode.Ok) ? alert(Messages.deletedMessage) : alert(error.message);
        this._view.isOpenModal = false;
        this.getProducts();
    }

    showContextMenu(id: string): void {
        this._view.selectedContextMenu = id;
    }

    showModalConfirmation(productName: string, productId: string): void {
        this._view.isOpenModal = true;
        this._view.selectedProductName = productName;
        this._view.selectedContextMenu = "empty";
        this._view.productId = productId;
    }

    processDelete(): void {
        this._interactor.processProductDelete(this._view.productId);
    }

    processDeleteConfirmation(response: HttpResponse<any>): void {
        if (response.status === HttpStatusCode.Ok) {
            alert(Messages.deletedMessage);
            this._view.isOpenModal = false;
        }
    }

    unsubscribe(): void {
        this._interactor.unsubscribe()
    }
}
```

Ejemplo de Interactor
```typescript
import { HttpErrorResponse, HttpResponse } from "@angular/common/http";
import { Inject, Injectable } from "@angular/core";
import { Products } from "@domain/entities/products.entitie";
import { IProductService } from "@domain/interfaces/product.interface";
import { Subscription } from "rxjs";
import { FunctionOneInputLogic, FunctionOneInteractorLogic } from "../view/model/functionone.model";

@Injectable()
export class FunctionOneInteractor implements FunctionOneInteractorLogic {
    private _presenter!: FunctionOneInputLogic;
    private _subscription!: Subscription;

    constructor(@Inject('productProviderService') private _functionOneService: IProductService) { }

    setPresenter(presenter: FunctionOneInputLogic): void {
        this._presenter = presenter;
    }

    processProductRequest(): void {
        this._subscription = this._functionOneService.getProducts().subscribe({
            next: (response: HttpResponse<Products[]>) => this._presenter.processResponse(response),
            error: (error: HttpErrorResponse) => this._presenter.errorRequest(error)
        });
    }

    processProductDelete(id: string): void {
        this._subscription = this._functionOneService.deleteProduct(id).subscribe({
            next: (response: HttpResponse<any>) => this._presenter.processDeleteConfirmation(response),
            error: (error: HttpErrorResponse) => this._presenter.errorRequest(error)
        });
    }

    unsubscribe(): void {
        this._subscription.unsubscribe();
    }
}
```

# Inversion de Dependencia

Es importante establecer mecanismos que nos permitan no depender de los detalles de implementacion que pueden cambiar con el tiempo, uno de estos usando el principio de diseno **DIP**

Este principio nos dice que debemos depender de abstracciones mas no de concreciones.

Ejemplo de Codigo

abstraccion

```typescript
export interface IAuthRepository {
  authentication(payload: AuthRequest): Observable<HttpResponse<AuthResponse>>
}
```

Concrecion
```typescript
@Injectable()
export class AuthService implements IAuthRepository {

  constructor(private http: HttpClient) { }

  authentication(payload: AuthRequest): Observable<HttpResponse<AuthResponse>> {
    return this.http.post<AuthResponse>(`${environment.API}/api/SignIn`, payload, { observe: 'response' });
  }
}
```

DIP
```typescript
// Invertimos la dependencia de nuestro servicio hacia nuestra abstracion
constructor(
    @Inject('authProvider') private authService: IAuthRepository
  ) { }
```
