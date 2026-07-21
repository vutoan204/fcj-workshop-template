---
title: "Console - Cognito Authentication"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Amazon Cognito in the AWS Console (optional)

> Skip User Pool creation after deploying the Auth stack. Console layouts can change; preserve the configuration values rather than relying on exact button names.

## 1. Create the User Pool and SPA app client

AWS may show either a simplified setup page or a multi-step wizard. Both paths below produce the same configuration; complete only one.

### A. Simplified setup

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/) in `ap-southeast-1`.
2. Choose **Create user pool** or **Set up resources for your application**.
3. Under **Application type**, select **Single-page application (SPA)**.
4. Name the application `SmartImage-WebClient-staging`.
5. Under **Options for sign-in identifiers**, select only **Email**; clear Username and Phone number.
6. Enable **Enable self-registration**.
7. Under **Required attributes for sign-up**, select `email` and `name`.
8. Leave the return URL or managed-login option empty because this workshop uses custom React forms and Amplify Auth directly.
9. Choose **Create user directory**.

### B. Multi-step setup

1. Under **Configure sign-in experience**, select **Email** as the only sign-in option.
2. Under **Configure security requirements**, set a minimum password length of 8 and require uppercase, lowercase, digits, and symbols.
3. Set MFA to **Optional** and enable only **Authenticator apps/TOTP**; leave SMS disabled to match CDK.
4. Under **Configure sign-up experience**, enable self-service sign-up, require `email` and `name`, and enable automatic email verification.
5. Set account recovery to **Verified email** only.
6. Under **Configure message delivery**, **Send email with Cognito** is sufficient for the lab. A dedicated SES setup is only needed for production delivery.
7. Under **Integrate app**, enter `SmartImage-UserPool-staging` as the User Pool name.
8. Create a public app client named `SmartImage-WebClient-staging`, select **Don't generate a client secret**, and enable SRP (`ALLOW_USER_SRP_AUTH`).
9. Leave Hosted UI/managed login disabled while the frontend uses custom forms.
10. Review the configuration and choose **Create user pool**.

![SPA, email, and sign-up attribute settings](/images/5-Workshop/5.4-Cognito-Auth/cognito_userpool_setup.png)

## 2. Custom attribute

CDK currently creates a mutable String attribute named `custom:role` with a length of 1–20. It can be created to mirror the stack, but the application does not use it as the primary authorization source.

If the wizard exposes custom attributes:

1. Open the new User Pool and find **Sign-up experience** or **Sign-up**.
2. Under **Custom attributes**, choose **Add custom attribute**.
3. Enter `role` as a **String** attribute.
4. Set minimum length to `1`, maximum length to `20`, and allow the value to be changed (**Mutable**).
5. Choose **Save changes** or **Create**.

Custom attributes generally cannot be deleted or have their type changed after pool creation. If the current Console requires attributes in the creation wizard, return to that step rather than creating another unnecessary pool.

> **Important difference:** The frontend and backend check `cognito:groups`, not `custom:role`. The custom attribute may therefore be treated as optional in the Console path.

## 3. User groups

1. Open the User Pool and choose the **User groups** tab.
2. Choose **Create group**.
3. Enter `admin`, description `Administrators with moderation access`, precedence `0`, and choose **Create group**.
4. Choose **Create group** again.
5. Enter `user`, description `Regular users`, precedence `10`, and choose **Create group**.

The result should be:

| Group | Description | Precedence |
|---|---|---|
| `admin` | Administrators with moderation access | 0 |
| `user` | Regular users | 10 |

![Project Cognito groups](/images/5-Workshop/5.4-Cognito-Auth/cognito_groups_setup.png)

## 4. Create a test user and assign a group

1. Register through the frontend during Testing, or choose **Users** → **Create user** for a manual account.
2. For a Console-created user, enter a valid email and either send an invitation or mark the email verified, according to the lab scenario.
3. Open the user and choose **Add user to group**.
4. Select `user` for a regular account or `admin` for moderation testing.
5. Sign out and sign in again after changing groups so the frontend receives a new ID token containing `cognito:groups`.

The project has no Post Confirmation trigger that automatically adds new sign-ups to `user`; successful registration does not imply group membership.

## 5. Record integration values

1. Copy the **User Pool ID** from **User pool overview**.
2. Open **App integration** → **App clients**, select the client, and copy its **Client ID**.
3. Verify that the app client has no client secret and SRP authentication is enabled.
4. Use the User Pool ID for the API Gateway authorizer; use both IDs in Amplify environment variables.
