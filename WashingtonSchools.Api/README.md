# ODataDemo

Demo forked from [hassanhabib/ODataDemo](https://github.com/hassanhabib/ODataDemo) that was presented on [Channel 9](https://channel9.msdn.com/Shows/On-NET/Supercharging-your-Web-APIs-with-OData-and-ASPNET-Core) & YouTube with Hassan Habib and Jeremy Likness

[![Supercharging your WEB APIs with OData](https://img.youtube.com/vi/ZCDWUBOJ5FU/0.jpg)](https://www.youtube.com/watch?v=ZCDWUBOJ5FU)

This fork adds API versioning (via headers), and updates the nuget pagages to the current (latest) versions.

## Update for Database
Ensure you run (change startup.cs db config as needed) database update to create db!

`dotnet ef database update`

## Potential Draggon's 
One issue that lingers is Swagger, OData and swagger in this particular implementation don't seem to mix well.
There is much more code that's required to fully implement, starting with `VersionedODataModelBuilder modelBuilder`  
A question posted in YouTube asks Hassan for an update:

```

Júlio Almeida
Show now an integration of OData with Swagger and you will have my slow clap﻿

Martin Spasiuk
Same here, I added the OData to my controller, and works, but swagger don't. Im using net core 2.1﻿

Hassan Habib (04/02/2019)
@Martin Spasiuk I'm working with the OData team to get this officially released - should be out officially shortly.﻿

```
## For API Versioning

* Add nuget package `Microsoft.AspNetCore.Mvc.Versioning`
* Update Startup.cs
* Update Controllers with version attribute
* Set request header with the appropriate header:   
  `curl --request GET --url https://localhost:5001/api/students --header 'api-version: 2.0' `

**Startup.cs**

```
public void ConfigureServices(IServiceCollection services)
{
	services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Latest);
    services.AddApiVersioning(o => {
        o.ReportApiVersions = true;
        o.ApiVersionReader = new HeaderApiVersionReader("api-version");
    });
...
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    ....

    app.UseMvc(routeBuilder =>
    {
            routeBuilder.EnableDependencyInjection();
            routeBuilder.Expand().Select().Count().OrderBy().Filter();
        });
        app.UseApiVersioning();
    }
}
		
```

For controllers - add a Versioning attribute for the version desired, or `[ApiVersionNeutral]` for none
> Create unique namespace for controllers with multiple versions:

**SchoolsController.cs**
```
namespace WashingtonSchools.Api.Controllers.V2
{
    [Route("api/[controller]")]
    [ApiController]
    [ApiVersion("2.0")]
    public class SchoolsController : ControllerBase
```

## Sample REST Calls

* curl --request GET \
  --url https://localhost:5001/api/values 

* curl --request GET \
  --url https://localhost:5001/api/students?$select=name,score \
  --header 'api-version: 1.0' 

* curl --request GET \
  --url https://localhost:5001/api/schools?$select=name \
  --header 'api-version: 2.0' 
