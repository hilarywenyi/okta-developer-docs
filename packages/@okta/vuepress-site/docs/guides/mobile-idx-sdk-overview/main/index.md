---
title: Overview of the mobile Identity Engine SDK
---

<div class="oie-embedded-sdk">

Design the implementation of the sign-in flow for your mobile app by understanding the representation of the flow, and with examples of the common parts.

<ApiLifecycle access="ie" /><br>

## Introduction

Integrate the Okta sign-in flow into your app in one of two ways: redirect the request to Okta which handles the flow in its own window, or handle each step in the flow by building the appropriate user interface elements, capturing the information, and updating Okta.

This guide is an overview of building your own user interface. For information on the redirect model, see <StackSnippet snippet="redirectquickstart" inline />.

## Sign-in flow

Okta supports many ways of authenticating the identity of a user during the sign-in flow. An Okta org administrator creates _policies_, different mixes of authentication methods (also called factors), and assigns them to apps, people, groups, and more. Policies also configure how many authentication methods are required (multifactor), and which methods are optional. This results in a large number of possible combinations for the different factors and steps to complete the sign-in flow.

The Android and iOS Identity Engine SDKs represent the sign-in flow as a state machine. You initialize the machine with the details of your Okta Application Integration, request the initial step in the flow, and cycle through responding to steps until either the user is signed in, cancels, or an error occurs.

<div class="common-image-format">

![A diagram showing the sign-in flow.](/img/mobile-sdk/mobile-idx-basic-flow.png "A diagram that shows the sign-in flow.")

</div>

Each sign-in step may include one or more possible user actions, such as:

- Choosing an authenticator, such as Okta Verify or security questions.
- Entering a One-Time Passcode (OTP).
- Cancelling the sign-in flow.

## Sign-in objects

Each step in the sign-in flow is represented by a number of different SDK objects:

<div class="common-image-format">

![A diagram showing the SDK objects and their relationships.](/img/mobile-sdk/mobile-idx-objects.png "A diagram that shows the SDK objects for the sign in flow and the relationships between them.")

</div>

- **Response:** The top-level object that represents a step and contains all the other objects. It also indicates a successful sign-in, and is used to send messages for cancelling the sign-in flow, or to retrieve an access token after the sign-in flow succeeds. A response may contain multiple authenticators and remediations.
- **Remediation:** Represents the main user actions for a step, such as enrolling in an authenticator or entering an OTP. It's also used to update values entered by the user and to request the next step in the flow.
- **Authenticator:** Represents a type, or factor for verifying the identity of a user, such as a username and password.
- **Method:** Represents a channel for an authenticator, such as using email or SMS for an OTP. An authenticator may have multiple methods.
- **Capability:** A user action, that's associated with a remediation, authenticator, or method, such as requesting a new OTP or a password reset.
- **Field:** Represents an item displayed in the UI, either a static element, such as a label, or a user input, such as a selection list. It also contains state information, such as whether the associated value is required. Options, or lists of choices, are represented by a collection of fields. A field may also contain a form that contains more fields.
- **Form:** Contains the fields that represent the user action for a remediation.


## Common parts of the flow

Some parts of the sign-in flow are the same:

- Add the SDK to your app.
- Configure the SDK.
- Initialize the client and start the flow.
- Process the response.
- Request a token.
- Sign the user out.

### Add the SDK

<StackSnippet snippet="adddependency" />

### Create and manage configurations

A _Configuration_ contains the settings used by the SDK to connect to an Okta Application Integration:

| Value         | Description |
| :------------ | :---------- |
| Issuer        | The Oauth 2.0 URL for the Okta org, such as `https://oie-123456.oktapreview.com/oauth2/default`. |
| Client ID     | The ID of the Okta Application Integration from the Okta Admin Console.  |
| Redirect URI  | A callback URI for launching the mobile app, such as `com.example.oie-123456:/callback`. |
| Scopes        | A space separated list of the requested access scopes for the app. For more information, see [Scopes in OpenID Connect & OAuth 2.0 API](https://developer.okta.com/docs/reference/api/oidc/#scopes).|

The configuration information in a shipping app is usually static. You can initialize the configuration values directly in the code or by reading them in from a file. During development you may want to provide a way to edit configuration values.

<StackSnippet snippet="loadingaconfiguration" />

### Start sign-in

Start the sign-in flow by creating an SDK client using a configuration, and then requesting the first step.

<StackSnippet snippet="initializingsdksession" />

### Process the response

The steps for processing a response are:

1. Check for a successful sign-in flow.
1. Check for messages, such as an invalid password.
1. Check for remediations.
1. Process the remediations.
1. Process the authenticators.

After the user enters the required information, update the remediation and request the next step.

<StackSnippet snippet="processresponse" />

### Complete the sign in flow

Check the response for a successful sign in at each step of the flow. When the user is signed in, exchange the remediation for an access token, and then exit the flow.

<StackSnippet snippet="gettingatoken" />

### Sign out a user

Sign out a user by revoking the access token granted when the user signed in, and by revoking a refresh token if one exists.

<StackSnippet snippet="signingout" />

## Sign-in flow UI

There are two general approaches for adding the sign-in flow to your app: add a fixed number of authentication methods, possibly in a fixed order, or generate the user interface dynamically based on the response at each step.

For a fixed number of methods (a static UI), you create appropriate views and populate them from the response. Using a static UI introduces risk as both the authentication methods and the order may be changed at any time by an administrator of your Okta org. There are three requirements to reduce that risk:

- Update the code in the app before policy changes are enabled.
- The updated app can be distributed to users before the policy changes are enabled.
- Ensure that the app is updated on all your users' devices.

The usual way to satisfy the last condition is with an enterprise managed workforce app. Also note that there's additional risk as external events may require an immediate policy change that can result in the inability of mobile users to sign in.

For consumer apps, and to reduce the risk for enterprise apps, a safer choice is building the user interface from the current sign-in step, by presenting a dynamic UI.

For an example of implementing a dynamic UI, see <StackSnippet snippet="dynamicuisample" inline />.

</div>
