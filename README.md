# Sharepoint Custom Claim Provider

 How to setup a SharePoint custom claim provider

# Using Akamai as an Identity Provider for SharePoint

Provisioning and configuring a custom claims provider in SharePoint Server Edition Farm when using Akamai as the identity provider involves several steps. Below is a general outline of the process:

### Prerequisites

1. **SharePoint Server Farm**: Ensure that your SharePoint Server Farm is set up and running.
2. **Akamai Identity Provider**: Make sure you have access to the Akamai Identity Provider and its metadata, including the SAML 2.0 endpoint and public certificate.

### Steps

#### Configure Akamai Identity Provider

1. **Login to Akamai Admin Console**: Log in to your Akamai identity provider admin console.
2. **Create SAML 2.0 Application**: Create a new SAML 2.0 application and configure it with the necessary attributes and claims that you want to pass to SharePoint.
3. **Download Metadata**: Download the SAML metadata XML file or make note of the SAML 2.0 endpoint and public certificate.

#### Import Akamai Certificate into SharePoint

1. **Open SharePoint Management Shell**: Run it as an administrator.
2. **Import Certificate**: Use PowerShell to import the Akamai public certificate into SharePoint.

    ```powershell
    $certPath = "C:\Path\To\AkamaiCert.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certPath)
    ```

#### Create a Trusted Identity Token Issuer

1. **Define Claim Mappings**: Define the claim types that you will be receiving from Akamai.

    ```powershell
    $emailClaimMap = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    ```

2. **Create Trusted Token Issuer**: Create a new trusted identity token issuer in SharePoint.

    ```powershell
    $realm = "urn:sharepoint:yourapp"
    $signInURL = "https://your-akamai-idp-endpoint/sso/saml2"
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AkamaiProvider" -Description "Akamai SAML Provider" -Realm $realm -ImportTrustCertificate $cert -ClaimsMappings $emailClaimMap -SignInUrl $signInURL -IdentifierClaim $emailClaim.url
    ```

#### Configure SharePoint Web Application to Use Akamai Provider

1. **Central Admin**: Go to SharePoint Central Administration.
2. **Manage Web Applications**: Navigate to "Manage web applications" under "Application Management".
3. **Authentication Providers**: Select your web application and click on "Authentication Providers" in the ribbon.
4. **Edit Authentication**: Choose your zone (usually "Default") and then select "Trusted Identity Provider" and choose "AkamaiProvider" (or whatever you named it).
5. **Save**: Click "Save" to apply the changes.

#### Test the Configuration

1. **Access SharePoint Site**: Try to access your SharePoint site. You should be redirected to the Akamai login page.
2. **Login**: After successful login, you should be redirected back to SharePoint and granted access based on the claims provided by Akamai.

### Troubleshooting

- Check SharePoint ULS logs for any authentication errors.
- Use tools like Fiddler to trace the SAML assertions and confirm they are being passed correctly.

This is a general guide and the exact steps may vary based on your specific requirements and SharePoint version. Always refer to the official documentation for the most accurate and detailed information.
