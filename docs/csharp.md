# C\#

## Implicitly-typed local variables
The `var` keyword allows the compiler to infer the variable's type.
One should keep in mind:

- Is the type clear given the context?
- Should emphasis be given to a types nullability?

### Examples of when to use `var`
A good reason is to improve readability. Without `var`, declarations are more verbose.
Multiple verbose delcarations can hurt readability.

Take the following examples:

In this example, it is clear that `HttpResponseMessage` is not nullable 
(for those with experience of the library).
```csharp
var response = await client.SendAsync(uri);
```
Following on, the deserialized response type would actually be inferred as `T?`.
Indeed we can see the type passed to the generic method `ReadFromJsonAsync<T>()`.
`var` is fine here because of a clear null check following deserialization.

```csharp
var response = await client.GetAsync(uri);

var content = await response.Content.ReadFromJsonAsync<T>(serializerOptions);
if (content == null)
{
    // do something...
} 

```


### When to avoid `var`
#### Example 2

Suppose we declare a variable; `organization`, and assign it to another variable.
```csharp
var organization = response.Content;
```
Depending on the context, `organization` could represent a `class` with properties like
Address, CEO, and PhoneNumber; or `organization` could be a `string`, representing the organisation's name.

Well-written the context ought to be obvious. But failing this what other
options exist?

- We could rename the variable to `organizationName`.
- We could explicitly declare the type to be a `string`.



#### Example 3

Suppose a variable's 'null state' determines some kind of logic.

```csharp title="Explicit types for emphasis"
int? result = reader.ReadAsNullableInt(); 
// Custom extension method because usually a DbReader throws so checking Db.Null first is good.
```
If `result` is `null`, a particular value might be shown to the user. 
A better design might use some kind of discriminated union, nonetheless, this is a common case.

or

```csharp
string result = reader.ReadAsString();
```
Those familiar with DbReader know that it throws on null. Perhaps for that reason, this is not the 
best example. it's also not the best example as the extension method alludes to the type. 
Regardless of the example, we might wish to empasise that a value can or cannot be null.
Explicitly declaring the type allows for this as implictly-typed variables are always nullable.