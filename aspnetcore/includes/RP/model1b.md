<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="8b0f1-101">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8b0f1-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8b0f1-102">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8b0f1-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="8b0f1-103">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="8b0f1-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8b0f1-104">`[DataType(DataType.Date)]`:  El atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (Date).</span><span class="sxs-lookup"><span data-stu-id="8b0f1-104">`[DataType(DataType.Date)]`:  The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="8b0f1-105">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="8b0f1-105">With this attribute:</span></span>

  * <span data-ttu-id="8b0f1-106">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="8b0f1-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8b0f1-107">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="8b0f1-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8b0f1-108">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="8b0f1-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
