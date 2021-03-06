# Providing Access to Externally Authenticated Users \(Identity Federation\)<a name="id_roles_common-scenarios_federated-users"></a>

Your users might already have identities outside of AWS, such as in your corporate directory\. If those users need to work with AWS resources \(or work with applications that access those resources\), then those users also need AWS security credentials\. You can use an IAM role to specify permissions for users whose identity is federated from your organization or a third\-party identity provider \(IdP\)\.

## Federating Users of a Mobile or Web\-based App with Amazon Cognito<a name="w3ab1c19c23c14c30b5"></a>

If you create a mobile or web\-based app that accesses AWS resources, the app needs security credentials in order to make programmatic requests to AWS\. For most mobile application scenarios, we recommend that you use [Amazon Cognito](https://aws.amazon.com/cognito/)\. You can use this service with the [AWS Mobile SDK for iOS](https://aws.amazon.com/sdkforios/) and the [AWS Mobile SDK for Android and Fire OS](https://aws.amazon.com/sdkforandroid/) to create unique identities for users and authenticate them for secure access to your AWS resources\. Amazon Cognito supports the same identity providers as those listed in next section, and it also supports [developer authenticated identities](http://aws.amazon.com/blogs/mobile/amazon-cognito-announcing-developer-authenticated-identities) and unauthenticated \(guest\) access\. Amazon Cognito also provides APIs for synchronizing user data so that it is preserved as users move between devices\. For more information, see [Using Amazon Cognito for Mobile Apps](id_roles_providers_oidc_cognito.md)\. 

## Federating Users with Public Identity Service Providers or OpenID Connect<a name="w3ab1c19c23c14c30b7"></a>

Whenever possible, use Amazon Cognito for mobile and web\-based application scenarios\. Amazon Cognito does most of the behind\-the\-scenes work with public identity provider services for you\. It works with the same third\-party services and also supports anonymous sign\-ins\. However, for more advanced scenarios, you can work directly with a third\-party service like Login with Amazon, Facebook, Google, or any IdP that is compatible with OpenID Connect \(OIDC\)\. For more information about using web identity federation using one of these services, see [About Web Identity Federation](id_roles_providers_oidc.md)\.

## Federating users with SAML 2\.0<a name="w3ab1c19c23c14c30b9"></a>

If your organization already uses an identity provider software package that supports SAML 2\.0 \(Security Assertion Markup Language 2\.0\), you can create trust between your organization as an identity provider \(IdP\) and AWS as the service provider\. You can then use SAML to provide your users with federated single\-sign on \(SSO\) to the AWS Management Console or federated access to call AWS APIs\. For example, if your company uses Microsoft Active Directory and Active Directory Federation Services, then you can federate using SAML 2\.0\. For more information about federating users with SAML 2\.0, see [About SAML 2\.0\-based Federation](id_roles_providers_saml.md)\.

## Federating users by creating a custom identity broker application<a name="w3ab1c19c23c14c30c11"></a>

If your identity store is not compatible with SAML 2\.0, then you can build a custom identity broker application to perform a similar function\. The broker application authenticates users, requests temporary credentials for users from AWS, and then provides them to the user to access AWS resources\. 

For example, Example Corp\. has many employees who need to run internal applications that access the company's AWS resources\. The employees already have identities in the company identity and authentication system, and Example Corp\. doesn't want to create a separate IAM user for each company employee\.

Bob is a developer at Example Corp\. To enable Example Corp\. internal applications to access the company's AWS resources, Bob develops a custom identity broker application\. The application verifies that employees are signed into the existing Example Corp\. identity and authentication system, which might use LDAP, Active Directory, or another system\. The identity broker application then obtains temporary security credentials for the employees\. This scenario is similar to the previous one \(a mobile app that uses a custom authentication system\), except that the applications that need access to AWS resources all run within the corporate network, and the company has an existing authentication system\.

To get temporary security credentials, the identity broker application calls either `AssumeRole` or `GetFederationToken` to obtain temporary security credentials, depending on how Bob wants to manage the policies for users and when the temporary credentials should expire\. \(For more information about the differences between these APIs, see [Temporary Security Credentials](id_credentials_temp.md) and [Controlling Permissions for Temporary Security Credentials](id_credentials_temp_control-access.md)\.\) The call returns temporary security credentials consisting of an AWS access key ID, a secret access key, and a session token\. The identity broker application makes these temporary security credentials available to the internal company application\. The app can then use the temporary credentials to make calls to AWS directly\. The app caches the credentials until they expire, and then requests a new set of temporary credentials\. The following figure illustrates this scenario\.

![\[Sample workflow using a custom identity broker application\]](http://docs.aws.amazon.com/IAM/latest/UserGuide/images/enterprise-authentication-with-identity-broker-application.diagram.png)

This scenario has the following attributes:

+ The identity broker application has permissions to access IAM's token service \(STS\) API to create temporary security credentials\.

+ The identity broker application is able to verify that employees are authenticated within the existing authentication system\.

+ Users are able to get a temporary URL that gives them access to the AWS Management Console \(which is referred to as single sign\-on\)\.

To see a sample application similar to the identity broker application that is described in this scenario, go to [Identity Federation Sample Application for an Active Directory Use Case](https://aws.amazon.com/code/1288653099190193) at *AWS Sample Code & Libraries*\. For information about creating temporary security credentials, see[Requesting Temporary Security Credentials](id_credentials_temp_request.md)\. For more information about federated users getting access to the AWS Management Console, see [Enabling SAML 2\.0 Federated Users to Access the AWS Management Console](id_roles_providers_enable-console-saml.md)\.