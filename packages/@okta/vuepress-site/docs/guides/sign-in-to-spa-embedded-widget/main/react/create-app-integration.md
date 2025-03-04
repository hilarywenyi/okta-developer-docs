Before integrating Okta authentication into your React app, obtain the OpenID Connect client ID from your Okta org by creating an app integration. You can create an app integration through the [Okta CLI](https://cli.okta.com/), the [Okta Apps API](/docs/reference/api/apps/), or through the [Admin Console](/docs/concepts/okta-organizations/#admin-console).

1. To create an Okta app integration that represents your React app, sign in to [your Admin Console](https://login.okta.com).
2. From the side navigation, select **Applications** > **Applications**, and then click **Create App Integration**.
3. In the dialog that appears, select **OIDC - OpenID Connect** as the **Sign-in method**, **Single-Page Application** as the **Application type**, and then click **Next**.
4. Fill in the following new app integration settings, and then click **Save**:

    | Setting                | Value/Description                                    |
    | -------------------    | ---------------------------------------------------  |
    | App integration name   | Specify a unique name for your app.                  |
    | Grant types            | Select **Authorization Code**, **Interaction Code**, and  **Refresh Token**. |
    | Sign-in redirect URIs  | Specify your app URI for the callback redirect from Okta. For example: `http://localhost:8080/login/callback` for the sample app, or `http://localhost:3000/login/callback` for the default React app              |
    | Sign-out redirect URIs | Specify your app sign-out redirect URI. For example: `http://localhost:8080` for the sample app, or `http://localhost:3000` for the default React app                            |
    | Trusted Origins > Base URIs | Specify your app base URI for CORS. For example: `http://localhost:8080` for the sample app, or `http://localhost:3000` for the default React app|
    | Assignments   | Assign users for your app.                                |

5. Select the **Sign On** tab and scroll down to the **User authentication** section. New apps are automatically assigned the shared default [authentication policy](https://help.okta.com/okta_help.htm?type=oie&id=ext-about-asop). This policy has a catch-all rule that allows a user access to the app using either one or two factors, depending on your org setup.

6. For this use case, we want to use only the password factor. Click **Edit** and select the **Password only** [preset policy](https://help.okta.com/okta_help.htm?type=oie&id=ext-preset-auth-policies) to assign it to your app.

7. Click **Save**.

   > **Note:** Be sure to also [update the password authenticator policy rule](/docs/guides/oie-embedded-common-org-setup/nodejs/main/#update-the-password-authenticator-to-password-only) to not require any additional verification.

> **Note:** Cross-Origin Resource Sharing (CORS) is automatically enabled for the Trusted Origins base URI that you specified in the Admin Console. If you're using the [Okta CLI](https://cli.okta.com/manual/apps/create/) to create your SPA Okta app integration, CORS is also automatically enabled for your base URI. You can verify that both **CORS** and **redirect** are enabled for your app by reviewing the **Security** > **API** > **Trusted Origins** page in the Admin Console.

### Okta org app integration configuration settings

You need two pieces of information from your org and app integration for your React app:

* **Client ID**: From the **General** tab of your app integration, save the generated **Client ID** value.
* **Issuer**: From the **General** tab of your app integration, save the **Okta domain** value. Use your Okta domain value for the [issuer](/docs/guides/oie-embedded-common-download-setup-app/nodejs/main/#issuer) setting that represents the authorization server. Use `https://${yourOktaDomain}/oauth2/default` as the issuer for your app if you're using an Okta Developer Edition org. See [Issuer configuration](/docs/guides/oie-embedded-common-download-setup-app/nodejs/main/#issuer) if you want to use another Okta custom authorization server.

<br>