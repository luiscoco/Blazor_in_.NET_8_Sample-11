# Blazor in .NET 8: Sample-11

Let's focus on building a Blazor application with protected routes (requiring authentication) and explore ways to implement user authorization.

Example: App With Protected Dashboard

Features Demonstrated:

Authentication State: Managing and tracking if a user is logged in

Authorization: Controlling access to specific routes/components based on user state

Cascading Authentication State: Making authentication data available to components

Prerequisites

Basic understanding of authentication concepts. For this example, we'll simulate authentication state; integrating a real authentication system (e.g., IdentityServer) is a larger topic

Setup

Project: Create a new Blazor Server project (or reuse an existing one). You can name it "ProtectedRoutes"

Services

AuthenticationStateProvider.cs (Inside a Services folder)

C#
namespace ProtectedRoutes.Services;

// Simulating an Auth provider
public class CustomAuthenticationStateProvider : AuthenticationStateProvider
{
    private bool _isAuthenticated = false;

    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = _isAuthenticated ? new ClaimsIdentity("testUser") : new ClaimsIdentity();
        var user = new ClaimsPrincipal(identity); 
        return Task.FromResult(new AuthenticationState(user)); 
    }

    public void SetAuthenticationState(bool loggedIn)
    {
        _isAuthenticated = loggedIn;
        NotifyAuthenticationStateChanged(GetAuthenticationStateAsync());
    }
}
Usa el código con precaución.

Components

AuthorizedRouteView.razor (Shared folder)

Razor CSHTML
@inherits RouteView

<CascadingAuthenticationState>
    <AuthorizeRouteView RouteData="@RouteData" DefaultLayout="@typeof(MainLayout)">
        <NotAuthorized>
            <h3>You are not authorized to view this page.</h3>
            <a href="/login">Login</a>
        </NotAuthorized>
    </AuthorizeRouteView>
</CascadingAuthenticationState>
Usa el código con precaución.

Login.razor (Pages folder)

Razor CSHTML
@page "/login"
@inject CustomAuthenticationStateProvider AuthProvider

<h3>Login</h3>

<button @onclick="SimulateLogin" class="btn btn-primary">Login</button>

@code {
    private void SimulateLogin()
    {
        AuthProvider.SetAuthenticationState(true); 
    }
}
Usa el código con precaución.

Dashboard.razor (Pages folder)

Razor CSHTML
@page "/dashboard"
@attribute [Authorize] 

<h3>Secure Dashboard</h3>
<p>Welcome to the protected area!</p>
Usa el código con precaución.
In your App.razor:

Razor CSHTML
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizedRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" /> 
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>

@code { 
    protected override void OnInitialized()
    {
        builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthenticationStateProvider>();
    }
}

Usa el código con precaución

Let's enhance this! Would you like to explore the following?

Integrating with a real authentication provider

Creating finer-grained authorization using roles

Protecting specific components instead of just entire routes


