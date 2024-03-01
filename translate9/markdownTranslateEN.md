# Avoid the AOP: Array-Oriented Programming



## Introduction
Arrays are a convenient way of organizing and passing data in **PHP** applications, but should we do it? In this article, I'll show the cons of abusing the use of associative arrays and a simple alternative for that.



## What Are Associative Arrays
**Associative Arrays, aka, Dictionaries,** in PHP are a type of array that uses key-value pairs for organizing and storing data in a structured format. These arrays allow us to manage data efficiently with keys instead of conventional index values like 0,1,2 etc., enabling more intuitive, human-readable elements.

Each key in an associative array in PHP is unique and can only refer to a single value. These keys can be named anything you wish, provided they are either a string or an integer, allowing for a flexible method to access and manipulate data.

For instance, you can assign employee names as keys and their related information as values. Associative arrays greatly simplify the process of data handling in PHP.



## Advantages of Using Associative Arrays
**Associative Arrays** in PHP come with several advantages. First of all, they possess immense flexibility as they allow the use of strings for specifying indexes instead of restricting to numeric values alone. This makes code more readable and accessible as developers can use meaningful names or descriptions as keys.

They can, therefore, simplify data search and management since you can access values directly through specific keys, eliminating the need for iteration over elements. This provides faster and more efficient data retrieval, especially when dealing with large data sets.

Besides, PHP provides a plethora of **built-in functions** that can handle associative arrays effectively, thus accelerating the process of sorting, merging, dividing, and filtering arrays.



## AOP - Array-Oriented Programming
You probably already heard about **OOP (Object-Oriented Programming)**, but what about **AOP (Array-Oriented Programming)**? This is not a "real" concept of Programming, but this is pretty common in a lot of PHP applications. I call **AOP** when a codebase is using **Associative Arrays** all over the place.

Let's take a look at an example where using **Associative Arrays** can be harmful to your codebase when it starts to grow or when the team working on it starts to grow.

If you already worked with any PHP framework you're probably familiar that usually the data from the **HTTP Request** is usually returned as an array when we need to validate/use it. In the example below you can see how we can get the data from the request and pass it to a service class that will use the request data to get a list of products and filter it using **Laravel**.

```php
// ProductController.php

final class ProductController extends Controller
{
    public function __construct(private ProductService $service)
		{
		}
	
    public function index(ProductsListRequest $request): JsonResponse
		{
		    return response()->json($this->service->productsList($request->validated()));
		}
}

// ProductService.php

final class ProductService
{
    public function productsList(array $filters): Collection
		{
		    $query = Product::query()
				    ->select(['id', 'name', 'price']);
			
		    if (isset($filters['search'])) {
				    $query->where('name', 'LIKE', "%{$filters['search']}%");
				}
			
		    if (isset($filters['min_price'])) {
				    $query->where('price', '>=', $filters['min_price']);
				}
			
	      if (isset($filters['max_price'])) {
				    $query->where('price', '<=', $filters['max_price']);
				}
		}
}
```

At first look, there's nothing wrong with the code above, and the fact is that there's not if the team is a small team, following a standard workflow and the codebase is not huge. Even so, the **DX** is not the best one, because if someone needs to change the logic of the `productList`. method, the developer will need to also check the `ProductListRequest` class and be sure that it's updated with all changes that happen to the filters passed by the UI.

The main issue here and in all places where we use **Associative Arrays** is the same: you don't have any info about the data the array holds. It can be anything and even worse, each item in the array can be something completely different! The data has no context at all.

Imagine now working on a codebase with more than 100 endpoints and with 10 developers, all using the same approach as shown above. This could get really MESSY. I just say that because I already experienced it in a project I worked on. Doing maintenance on old code and onboarding new devs can become a nightmare.

Besides this example, another abusive use of **Associative Arrays** is on method returns like the example below:

```php
public function getUserContactInfo(int $userId): array
{
    $user = User::query()->firstOrFail($userId);
	
	   // LOGIC HERE
	
    return [
		    'street' => 'Foo',
			  'number' => 1,
			  'zip' => '123456',
			  'phone' => '123456789',
		];
}
```

Like the first example, at first, there's nothing wrong with the method above, but imagine that 1 year later someone goes into the code above and removes the `phone`, depending on how this is done and the team size, other developers can still think that the `phone` property is still there and cause issues or errors. Tests can cover that? Sure, but that's not an ideal DX for your team.



## Other Disadvantages
Another thing to keep in mind and that's a prime concern when dealing with **Associative Arrays** is memory use. They can be quite memory-intensive, particularly with large datasets, because they store additional information (the keys) in addition to the values. Consequently, performance can be adversely affected in applications that are not optimally coded.

Additionally, arrays in PHP are not strongly typed, which means you could unintentionally store different types of values in the same array, leading to potential errors or ineffective memory usage.



## Understanding Your Codebase
The main issue with the abusive use of **Associative Arrays** for me is that it makes your codebase **obscure**. It will always require additional work from whoever is working with that portion of the code to understand what type of data the array is holding.

So how do we fix this "issue"? That's pretty simple: start using **Data Transfer Objects (DTOs)**. DTOs can be a simple class used to map and type the data like the example below:

```php
final readonly class ProductFilters(
    public ?string $search = null,
    public ?float $min_price = null,
    public ?float $max_price = null,
) {}
```

With this **DTO**, we can update the code shown before to use it:

```php
final class ProductService
{
    public function productsList(ProductFilters $filters): Collection
		{
		    $query = Product::query()
				    ->select(['id', 'name', 'price']);
			
		    if (! blank($filters->search)) {
				    $query->where('name', 'LIKE', "%{$filters->search}%");
				}
			
		    if (! blank($filters->min_price)) {
				    $query->where('price', '>=', $filters->min_price);
				}
			
	      if (! blank($filters->max_price)) {
				    $query->where('price', '<=', $filters->max_price);
				}
		}
}
```

The code itself didn't change much, but the context of the data in that code is **TOTALLY DIFFERENT**. Now you have an object **ProductFilters** that's a **DTO** wrapping the filters you need and giving them context. If anyone needs to update anything in this method: **fix an issue or extend it with new filters**, it's a pretty simple and straightforward task because you have all the possible filters listed and you know exactly what kind of data each filter holds.

We can do the same for the second example with another really simple **DTO**:

```php
final readonly class UserContactInfo(
    public string $street,
	  public int $number,
	  public ?string $zip,
	  public ?string $phone,
) {}
```

Then we can update the `getUserContactInfo` to use it:

```php
public function getUserContactInfo(int $userId): UserContactInfo
{
    $user = User::query()->firstOrFail($userId);
	
	   // LOGIC HERE
	
    return new UserContactInfo(
		    street: 'Foo',
			  number: 1,
			  zip: '123456',
			  phone: '123456789',
		);
}
```

Again, the code itself is almost the same, but now the data is wrapped in a **DTO** that gives it context and provides a much better **DX** for the team to work with!



## Conclusion
Indeed, PHP is an exemplary language, renowned for its versatility and robustness in creating intricate web applications with relative ease. While the utility of **Associative Arrays** cannot be dismissed as they provide a flexible way to access and manipulate data, it is essential to consciously use them, understanding their potential drawbacks as I have highlighted some in this article.

However, every tool in programming has its place and purpose. **Associative Arrays** are no exception, and their powerful features can certainly outweigh the mentioned downsides in the correct contexts. After all, the choice to use them will largely depend on the specific requirements of the project in hand alongside careful consideration of the trade-offs. Judicious use of the language's features will always play a key role in crafting efficient and powerful PHP applications.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya!