# Activity-25-SOLID-PRINCIPLES-IN-Angular

## Understanding SOLID Principles and Their Application in Angular

The SOLID principles are foundational concepts in object-oriented programming that encourage more maintainable, flexible, and testable code. These principles can be especially useful when applied to Angular projects, where components, services, and modules all work together in a highly modular way. Let's explore each principle in detail and see how they apply in Angular development.

##

## 1. Single Responsibility Principle (SRP)

Explanation:

 is a fundamental principle of software engineering which states that a class or module should have only one reason to change. In other words, it should have only one responsibility. Violating this principle can lead to code that is difficult to maintain and understand.

For Example 

Consider a component that displays a list of products and allows the user to filter them by category.
 
 ``` typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {

  products: any[];
  categories: string[];
  selectedCategory: string;

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.products = this.productService.getProducts();
    this.categories = this.productService.getCategories();
  }

  filterByCategory(category: string) {
    this.selectedCategory = category;
    this.products = this.productService.getProductsByCategory(category);
  }

}
```
To fix this violation, we can refactor the component into two separate components, each with its own responsibility.

Fixing the Violation

First, we’ll create a new component that displays the list of products:

``` typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {

  products: any[];

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.products = this.productService.getProducts();
  }

}

```
This component is now responsible only for displaying the list of products. We’ve removed the category filtering logic to a new component.

Next, we’ll create a new component that handles the category filtering:

``` typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-filter',
  templateUrl: './product-filter.component.html',
  styleUrls: ['./product-filter.component.css']
})
export class ProductFilterComponent implements OnInit {

  categories: string[];
  selectedCategory: string;

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.categories = this.productService.getCategories();
  }

  filterByCategory(category: string) {
    this.selectedCategory = category;
    this.productService.getProductsByCategory(category);
  }

}
```
This component is now responsible only for handling the category filtering. It updates the selected category and calls the getProductsByCategory() method of the ProductService to update the list of products.

Finally, we’ll update the app.component.html file to include both components:

``` typescript
<app-product-filter (categorySelected)="productList.filterByCategory($event)"></app-product-filter>
<app-product-list #productList></app-product-list>
```
The app-product-filter component emits a categorySelected event when a new category is selected. This event is handled by the app-product-list component, which calls its filterByCategory() method with the selected category.


 ##

 ## 2. Open/Closed Principle (OCP)

 Explanation
 
 This principle states that an entity should be open for extension, but closed for modification. In other words, when creating new features we should incorporate/extend/build on top of existing entities, not modify those existing entities.

For Example 

 a simple ButtonComponent:

``` typescript
@Component({
    selector: 'app-button',
    template: `
        <button>Hi</button>
    `
})
export class ButtonComponent {}
```
We could modify our component to this by accepting an input:

For Example 

``` typescript
@Component({
  selector: 'app-button',
  template: `
      <button [style.backgroundColor]="color">Hi</button>
  `,
  standalone: true
})
export class ButtonComponent {
  @Input() color? = '#cecece';
}
```
Now we can supply it with a color:

``` typescript
<app-button color="blue"></app-button>
```
But, technically, this is a violation of the open-closed principle. Alternatively, we could create a directive to apply the button colour instead.

``` typescript
import { Directive, HostBinding, Input } from "@angular/core";

@Directive({
  selector: 'app-button[color]',
  standalone: true
})
export class ButtonColorDirective {
  @HostBinding('style.backgroundColor') @Input() color? = '#cecece';
}
```
This would adhere to the open-closed principle as we have extended the buttons functionality by applying a directive to it, we have not modified the underlying entity.


##

## 3. Liskov Substitution Principle (LSP)

Explanation:

 is a fundamental concept in software engineering that is essential for designing and implementing maintainable and extensible systems. LSP states that objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program. Violating the LSP can lead to unexpected behavior and bugs.

 For Example 

``` typescript
class Animal {
  move() {
    console.log('I can move');
  }
}

class Bird extends Animal {
  fly() {
    console.log('I can fly');
  }
}

class Ostrich extends Animal {
  move() {
    console.log('I can move, but I cannot fly');
  }
}

function moveAnimal(animal: Animal) {
  animal.move();
}

const bird = new Bird();
moveAnimal(bird); // outputs "I can move"

const ostrich = new Ostrich();
moveAnimal(ostrich); // outputs "I can move, but I cannot fly"
```
In this example, Ostrich is a subclass of Animal that overrides the move() method to reflect the fact that ostriches cannot fly. However, when we pass an instance of Ostrich to the moveAnimal() function, we get unexpected behavior because the move() method of Ostrich does not behave the same way as the move() method of Animal. This violates the LSP because Ostrich is not a true substitute for Animal.

To fix the violation, we can modify the Ostrich class to behave more like Animal:

``` typescript
class Ostrich extends Animal {
  move() {
    console.log('I can move, but I cannot fly');
  }

  fly() {
    throw new Error('I cannot fly');
  }
}
```
This ensures that Ostrich behaves like Animal and can be used as a substitute for it without affecting the correctness of the program.

The Liskov Substitution Principle is an important principle in software engineering that helps us design maintainable and extensible systems. Violating the LSP can lead to unexpected behavior and bugs, but these violations can be fixed by ensuring that subclasses behave like their superclasses.


##

## 4. Interface Segregation Principle (ISP)

Explanation:

is a software development principle that states that a software component should not be forced to depend on interfaces that it does not use. In other words, clients should not be forced to depend on methods they do not use. This principle helps to reduce coupling between components and makes code more modular, flexible, and maintainable.

For Example 

  a component needs to retrieve data from a service. The service provides several methods, including ones that are not required by the component.

``` typescript
interface DataService {
  getItems(): Observable<Item[]>;
  addItem(item: Item): Observable<Item>;
  updateItem(item: Item): Observable<Item>;
  deleteItem(id: number): Observable<boolean>;
}
```
The component only needs to retrieve items from the service, so it should only depend on the getItems method. However, if the component is written as follows:

``` typescript
class MyComponent {
  items: Item[];
  
  constructor(private dataService: DataService) {}
  
  ngOnInit() {
    this.dataService.getItems().subscribe(items => this.items = items);
  }
}
```
The component is depending on the DataService interface, which includes methods that are not used by the component. This can lead to unnecessary coupling and performance issues.

To fix this, we can create a new interface that only includes the getItems method and have the service implement this interface. The component can then depend on this new interface instead of the original DataService interface.

``` typescript
interface ItemsService {
  getItems(): Observable<Item[]>;
}

class DataService implements ItemsService {
  getItems() { ... }
  addItem(item: Item) { ... }
  updateItem(item: Item) { ... }
  deleteItem(id: number) { ... }
}

class MyComponent {
  items: Item[];
  
  constructor(private itemsService: ItemsService) {}
  
  ngOnInit() {
    this.itemsService.getItems().subscribe(items => this.items = items);
  }
}
```
Now, the component only depends on the ItemsService interface, which includes only the getItems method. This helps to reduce coupling and improves performance.


##

## 5. Dependency Inversion Principle (DIP)

Explanation:

is an important principle of object-oriented design. It suggests that high-level modules should not depend on low-level modules, but both should depend on abstractions. The abstractions should not depend on the details, but the details should depend on the abstractions.

For Example:

``` typescript
import { CustomerRepository } from './customer.repository';

@Injectable()
export class CustomerService {
  constructor(private customerRepository: CustomerRepository) {}

  getCustomers(): Observable<Customer[]> {
    return this.customerRepository.getCustomers();
  }
}
```
The implementation of CustomerRepository looks like this:

Violation of DIP

``` typescript
@Injectable()
export class CustomerRepository {
  private customers: Customer[] = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
    { id: 3, name: 'Jack' }
  ];

  getCustomers(): Observable<Customer[]> {
    return of(this.customers);
  }
}
```
The CustomerService class depends on the CustomerRepository class, which violates the Dependency Inversion Principle. The CustomerService class is a high-level module, and the CustomerRepository class is a low-level module. The CustomerService class should not depend on the CustomerRepository class directly.

Fixing Violation of DIP

To fix the violation of the Dependency Inversion Principle, we need to introduce an abstraction between the CustomerService class and the CustomerRepository class. We can introduce an interface ICustomerRepository to define the contract that the CustomerRepository class should implement.

``` typescript
import { ICustomerRepository } from './customer.repository';

@Injectable()
export class CustomerService {
  constructor(private customerRepository: ICustomerRepository) {}

  getCustomers(): Observable<Customer[]> {
    return this.customerRepository.getCustomers();
  }
}
```
The implementation of ICustomerRepository looks like this:

``` typescript
export interface ICustomerRepository {
  getCustomers(): Observable<Customer[]>;
}
```
The implementation of CustomerRepository now implements the ICustomerRepository interface.


``` typescript
@Injectable()
export class CustomerRepository implements ICustomerRepository {
  private customers: Customer[] = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
    { id: 3, name: 'Jack' }
  ];

  getCustomers(): Observable<Customer[]> {
    return of(this.customers);
  }
}
```
Now, the CustomerService class depends on the ICustomerRepository interface, and the CustomerRepository class implements the ICustomerRepository interface. This way, the CustomerService class is decoupled from the CustomerRepository class, and both depend on the abstraction.


##  Hashnode Link :

https://lorenzcamo.hashnode.dev/activity-25-solid-principles-in-angular
