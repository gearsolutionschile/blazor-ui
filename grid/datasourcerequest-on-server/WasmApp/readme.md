# Use DataSourceRequest and DataSourceResult in a WASM Blazor app

The Telerik `DataSourceRequest` must be serialized to the server, so you can use it to obtain the needed data as per its contents (needed page, page size, filter, sort, etc.).

You can obtain the data through extension methods provided by the `Telerik.DataSource` package - the `.ToDataSourceResult(dataSourceRequest)` and `.ToDataSourceResultAsync(dataSourceRequest)`. They can work with collections like `List<T>`, `IEnumerable<T>` and `IQueriable<T>`. You can use `IQueriable` collections coming from another service (such as EntityFramework) to perform optimized queries - the Telerik methods use LINQ expressions internally so that the framework can resolve them in the most efficient manner.

There are a few specifics to this scenario:

* The `Server` project must reference the `Telerik.DataSource` package and you need the `using` statements to get the extension methods.
* The serialization and deserialization of the data must be handled by the app. The `DataSourceRequest` object can be serialized and deserialized through the built-in `System.Text.Json` serializer that comes with Blazor. The client-side app must send it as a string, and the controller must deserialize it from this string.
* There is a data envelope class used in the project where we serialize the total count and the current page of data. This is necessary, because the framework cannot deserialize the `IEnumerable` object the Telerik `DataSourceResult` has - a typed collection is needed - `System.Text.Json` cannot successfully deserialize interface properties. Perhaps a future version of the framework will be able to perform this deserialization and will simplify the data return to something similar to the server app approach where you only return the `DataSourceResult` object.

This example uses a service in the WASM app to call the API endpoint. For such a small example this may be a little too much, but a real app can implement additional logic there (such as caching or offline capabilities for a PWA, or more methods suitable to more components that boil down to similar HTTP calls). Feel free to use the architecture you prefer.