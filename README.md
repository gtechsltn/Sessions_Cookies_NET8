# Sessions and Cookies in .NET 8.0

Working with Sessions and Cookies in ASP.NET Core

https://medium.com/@nwonahr/working-with-sessions-and-cookies-in-asp-net-core-013b24037d91

## Install
```
dotnet add package Microsoft.AspNetCore.Session
```

## Configure the Middleware
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDistributedMemoryCache();
    services.AddSession(options =>
    {
        options.IdleTimeout = TimeSpan.FromMinutes(30); // Set session timeout
        options.Cookie.HttpOnly = true; // Ensures the session cookie is accessible only by the server
        options.Cookie.IsEssential = true; // Required for GDPR compliance
    });
    services.AddControllersWithViews();
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }
    
    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseRouting();

    app.UseSession(); // Add the session middleware

    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

## Storing and Retrieving Data in Session
```
// Storing data in session
HttpContext.Session.SetString("Username", "JohnDoe");

// Retrieving data from session
var username = HttpContext.Session.GetString("Username");
```

## Adding a Cookie 
```
// Adding a cookie
HttpContext.Response.Cookies.Append("Username", "JohnDoe", new CookieOptions
{
    Expires = DateTimeOffset.UtcNow.AddMinutes(30),
    HttpOnly = true, // Accessible only by the server
    IsEssential = true // Required for GDPR compliance
});
```

## Retrieving a Cookie
```
// Retrieving a cookie
string username = HttpContext.Request.Cookies["Username"];
```

## Deleting a Cookie
```
// Deleting a cookie
HttpContext.Response.Cookies.Delete("Username");
```

## Best Practices
+ Security: Always ensure that cookies are marked as HttpOnly to prevent access by client-side scripts. Consider using Secure and SameSite attributes to further enhance security.
+ Session Management: Be mindful of the session timeout and its impact on user experience. Session data should be kept minimal and sensitive information should be avoided.
+ GDPR Compliance: If your application serves European users, ensure that your use of cookies complies with GDPR regulations. This may involve providing users with clear information about the use of cookies and obtaining their consent.
