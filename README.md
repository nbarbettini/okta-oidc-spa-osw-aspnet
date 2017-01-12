# Introduction  
This code sample demonstrates how to use Okta's OpenID Connect implementation with an ASP.NET Single Page Application calling an ASP.NET Web API.

## Solution architecture  
The Visual Studio solution is made of 2 projects:  
- **SinglePageWebApp** is a single page web application where most (if not all) of the action occurs in the Views/Home/Index.cshtml page (along with the Scripts/single-page.js JavaScript file). This project is the __Client__ in OAuth2 lingo.
- **WebApi** is an ASP.NET Web API project that exposes 2 endpoints:  **/unprotected** and **/protected**. The **/unprotected** endpoint can be called by anonymous or non-authorized users, but the **/protected** endpoint can only be accessed by authenticated users who are members of pre-configured Okta groups. This project is the __Resource Server__ in OAuth2 lingo.


## Pre-requisites

- This sample was built on Windows Server 2012 and Visual Studio 2015 Community Edition. Although we don't make Visual Studio 2015 a requirement, we highly recommend that you use it to test this application.

# How to run this sample
1. Clone the repository source code and make sure you can compile it in Visual Studio 2015.
3. If you haven't done so yet, create an OpenID Connect client app in your Okta organization. Navigate to the ``Admin`` dashboard and then to `Applications -> Add Application -> Create New App`.
4. In the `Platform` dropdown list, select the `Single Page App (SPA)` option. The sign-on option should then default to `OpenID Connect`. If you don't see it, please contact Okta at `developers AT okta DOT com` to have OpenID Connect enabled in your Okta organization.
4. Press the `Create` button.
5. Fill out the `Application Name` field.
6. Add a Redirect URI to be the URL of your SinglePageWebApp application (the default value we use is `http://localhost:8080/`) and press `Finish`.
7. In the `Sign On` tab of your OpenID Connect client app, click `Edit` in the `OpenID Connect ID Token` section.
8. On the `Groups claim` line, leave the default `groups` claim name and select the `Regex` value in the dropdown box  instead of `Starts with`. 
9. In the blank text field following `Regex`, enter `.*`. This is the regular expression that includes all the Okta-mastered groups the user is a member of in the ID Token. Press `Save` to save your ID Token settings.
10. In the General tab, copy the Client ID value in the `Client Credentials` section and paste it into both **web.dev.release.config** configuration files for the **okta:ClientId** parameter (in the `SinglePageWebApp` and `WebApi` Visual Studio projects).
11. Go to the `Security -> API -> CORS` (or `Security -> API -> Trusted Origins`) tab and add the url of your application (such as `http://localhost:8080`) as CORS endpoint. 
12. Assign test users to this new application (they are the users you will use to test the application).
13. Open the **web.dev.release.config** in the **SinglePageWebApp** and make sure to properly fill out the **okta:TenantUrl** parameter with the url of your Okta organization (such as _https://company.oktapreview.com_, not _https://company-admin.oktapreview.com_).  
You may also duplicate this file and create a new **web.dev.debug.config** file if you would rather run the projects in debug mode. 
14. In the **web.dev.release.config** of the `WebApi` project, update the following parameters:  
  - `okta:OIDC_Issuer`: this parameter must match the url of your Okta organization (such as _https://company.oktapreview.com_), because the issuer claim of Okta's OpenID Connect ID tokens has an issuer value that is the url of the Okta organization
- `okta:RequiredGroupMemberships`: this parameter should include the names of the possible group(s) your test users are assigned to (comma-separated list). The `OktaGroupMembershipAttribute` custom class in the `WebApi` project will validate if the user OIDC token contains  any of these groups (not all of them).
10. Rebuild the whole solution (in _Release_ mode if you used _web.dev.release.config_, in _Debug_ mode if you used _web.dev.debug.config_),  to push your configuration changes into the projects' web.config files.
12. If you want to look more closely at the code, you should check the following files  in the  **SinglePageWebApp** project:
  - `Views/Home/SignIn.cshtml` page 
  - `Scripts/single-page.js` file   

 and in the __WebApi__ project:
  - `Startup.cs` to understand how the resource server plugs the OWIN Oauth Bearer Authentication middleware into the authentication pipeline
  - `ValuesController.cs` to understand how the resource server processes the requests to `/unprotected` and `/protected`
  - `OktaGroupMembershipAttribute.cs` to understand how the resource server checks the `groups` claim included in the ID token to perform authorization on the `/protected` endpoint.

  If you have any comment or question, don't hesitate to contact us at `developers AT okta DOT com`.