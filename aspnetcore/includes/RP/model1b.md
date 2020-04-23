<!-- THIS INCLUDE USED BY MVC AND RP -->
Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`:  El atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (Date). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.
