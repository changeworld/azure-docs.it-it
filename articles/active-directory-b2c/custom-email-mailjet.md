---
title: Verifica della posta elettronica personalizzata con Mailjet
titleSuffix: Azure AD B2C
description: Informazioni su come eseguire l'integrazione con Mailjet per personalizzare il messaggio di posta elettronica di verifica inviato ai clienti al momento dell'iscrizione per usare le Azure AD B2C abilitate per l'uso.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f48135523238711eb9058b35348895c851a95403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713823"
---
# <a name="custom-email-verification-with-mailjet"></a>Verifica della posta elettronica personalizzata con Mailjet

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Usare la posta elettronica personalizzata in Azure Active Directory B2C (Azure AD B2C) per inviare messaggi di posta elettronica personalizzati agli utenti che effettuano l'iscrizione per usare le applicazioni. Usando mailjet, il provider di posta elettronica di terze parti, è possibile usare il proprio modello di posta elettronica e l'indirizzo e l'oggetto *from:,* oltre a supportare la localizzazione e le impostazioni personalizzate di password una sola volta (OTP).

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

La verifica della posta elettronica personalizzata richiede l'uso di un provider di posta elettronica di terze parti, ad esempio [Mailjet,](https://Mailjet.com) [SendGrid](./custom-email-sendgrid.md)o [SparkPost,](https://sparkpost.com)un'API REST personalizzata o qualsiasi provider di posta elettronica basato su HTTP (incluso il proprio). Questo articolo descrive la configurazione di una soluzione che usa Mailjet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Creare un account Mailjet

Se non se ne ha già uno, iniziare configurando un account Mailjet (i clienti di Azure possono sbloccare 6.000 messaggi di posta elettronica con un limite di 200 messaggi di posta elettronica al giorno). 

1. Seguire le istruzioni di configurazione in [Creare un account Mailjet](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. Per poter inviare messaggi di posta elettronica, [registrare e convalidare l'indirizzo](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) di posta elettronica del mittente o il dominio.
2. Passare alla pagina [Gestione chiavi API](https://app.mailjet.com/account/api_keys). Registrare la **chiave API e** la chiave **privata** per l'uso in un passaggio successivo. Entrambe le chiavi vengono generate automaticamente quando viene creato l'account.  

> [!IMPORTANT]
> Mailjet offre ai clienti la possibilità di inviare messaggi di posta elettronica da indirizzi IP condivisi [e indirizzi IP dedicati.](https://documentation.mailjet.com/hc/articles/360043101973-What-is-a-dedicated-IP) Quando si usano indirizzi IP dedicati, è necessario creare correttamente la propria reputazione con un riscaldamento dell'indirizzo IP. Per altre informazioni, vedere [Ricerca per categorie warm up my IP ?](https://documentation.mailjet.com/hc/articles/1260803352789-How-do-I-warm-up-my-IP-).


## <a name="create-azure-ad-b2c-policy-key"></a>Creare Azure AD B2C chiave dei criteri

Archiviare quindi la chiave API Mailjet in una chiave Azure AD B2C criteri per fare riferimento ai criteri.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina **Panoramica** selezionare **Identity Experience Framework**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere **Manuale.**
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `MailjetApiKey`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere la chiave **API** mailtura registrata in precedenza.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere **Manuale.**
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `MailjetSecretKey`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere la chiave privata di Mail secret **registrata** in precedenza.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="create-a-mailjet-template"></a>Creare un modello di Mailsystems

Dopo la creazione di un account Mail jet e l'archiviazione della chiave [](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)API Mailsystem in una chiave Azure AD B2C criteri di posta elettronica, creare un modello transazionale dinamico di Mailsystems.

1. Nel sito Mailsystem aprire la pagina [dei modelli transazionali](https://app.mailjet.com/templates/transactional) e selezionare **Crea un nuovo modello.**
1. Selezionare **Codifica in HTML** e quindi selezionare Codice da **zero.**
1. Immettere un nome di modello `Verification email` univoco, ad esempio , e quindi selezionare **Crea.**
1. Nell'editor HTML incollare il modello HTML seguente o usare il proprio. I `{{var:otp:""}}` parametri e verranno `{{var:email:""}}` sostituiti dinamicamente con il valore della password una sola volta e l'indirizzo di posta elettronica dell'utente.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Espandere **Modifica oggetto** in alto a sinistra
    1. Per **Oggetto** immettere un valore predefinito per l'oggetto. Mail jet usa questo valore quando l'API non contiene un parametro subject.
    1. In **Nome digitare** il nome della società.
    1. Per Indirizzo **selezionare** l'indirizzo di posta elettronica
    1. Selezionare **Salva**.
1. Nell'angolo in alto a destra **selezionare Salva & Pubblica** e quindi **Sì, pubblica le modifiche**
1. Registrare **l'ID** modello del modello creato per l'uso in un passaggio successivo. Questo ID viene specificato quando si aggiunge [la trasformazione delle attestazioni](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Aggiungere Azure AD B2C tipi di attestazione

Nei criteri aggiungere i tipi di attestazione seguenti all'elemento `<ClaimsSchema>` all'interno di `<BuildingBlocks>` .

Questi tipi di attestazioni sono necessari per generare e verificare l'indirizzo di posta elettronica usando un codice OTP (One-Time Password).

```XML
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>Mailjet request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Aggiungere la trasformazione attestazioni

È quindi necessaria una trasformazione delle attestazioni per l'output di un'attestazione di stringa JSON che sarà il corpo della richiesta inviata a Mailjet.

La struttura dell'oggetto JSON è definita dagli ID nella notazione del punto di InputParameters e transformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dai valori di InputClaims e dalle proprietà "Value" degli InputParameters. Per altre informazioni sulle trasformazioni delle attestazioni JSON, vedere [Trasformazioni di attestazioni JSON.](json-transformations.md)

Aggiungere la trasformazione di attestazioni seguente all'elemento `<ClaimsTransformations>` all'interno di `<BuildingBlocks>` . Apportare gli aggiornamenti seguenti al codice XML della trasformazione attestazioni:

* Aggiornare il valore InputParameter con l'ID del modello transazionale Mailjet creato in precedenza `Messages.0.TemplateID` in Creare un modello [Mailjet](#create-a-mailjet-template).
* Aggiornare il `Messages.0.From.Email` valore dell'indirizzo. Usare un indirizzo di posta elettronica valido per impedire che il messaggio di posta elettronica di verifica venga contrassegnato come posta indesiderata.
* Aggiornare il valore del parametro `Messages.0.Subject` di input della riga dell'oggetto con una riga dell'oggetto appropriata per l'organizzazione.

```XML
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Aggiungere la definizione del contenuto DataUri

Sotto le trasformazioni delle attestazioni all'interno `<BuildingBlocks>` di aggiungere [l'elemento ContentDefinition](contentdefinitions.md) seguente per fare riferimento all'URI dati della versione 2.1.2:

```XML
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Creare un controllo DisplayControl

Un controllo di visualizzazione della verifica viene usato per verificare l'indirizzo di posta elettronica con un codice di verifica inviato all'utente.

Questo controllo di visualizzazione di esempio è configurato per:

1. Raccogliere il `email` tipo di attestazione dell'indirizzo dall'utente.
1. Attendere che l'utente fornirà il tipo `verificationCode` di attestazione con il codice inviato all'utente.
1. Restituire `email` l'oggetto al profilo tecnico autoassato che contiene un riferimento a questo controllo di visualizzazione.
1. Usando `SendCode` l'azione , generare un codice OTP e inviare un messaggio di posta elettronica con il codice OTP all'utente.

   ![Azione invia codice di verifica tramite posta elettronica](media/custom-email-mailjet/display-control-verification-email-action-01.png)

Nelle definizioni del contenuto, sempre `<BuildingBlocks>` all'interno di , aggiungere il [displayControl](display-controls.md) seguente di tipo [VerificationControl](display-control-verification.md) ai criteri.

```XML
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Aggiungere profili tecnici OTP

Il `GenerateOtp` profilo tecnico genera un codice per l'indirizzo di posta elettronica. Il `VerifyOtp` profilo tecnico verifica il codice associato all'indirizzo di posta elettronica. È possibile modificare la configurazione del formato e la scadenza della password una sola volta. Per altre informazioni sui profili tecnici OTP, vedere [Definire un profilo tecnico con password una sola volta.](one-time-password-technical-profile.md)

Aggiungere i profili tecnici seguenti `<ClaimsProviders>` all'elemento .

```XML
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Aggiungere un profilo tecnico dell'API REST

Questo profilo tecnico dell'API REST genera il contenuto del messaggio di posta elettronica usando il formato Mail passport. Per altre informazioni sui profili tecnici RESTful, vedere [Definire un profilo tecnico RESTful.](restful-technical-profile.md)

Come per i profili tecnici OTP, aggiungere i profili tecnici seguenti `<ClaimsProviders>` all'elemento .

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Creare un riferimento a DisplayControl

Nel passaggio finale aggiungere un riferimento all'oggetto DisplayControl creato. Sostituire i profili `LocalAccountSignUpWithLogonEmail` tecnici `LocalAccountDiscoveryUsingEmailAddress` esistenti e autosito con quanto segue. Se è stata usata una versione precedente di Azure AD B2C criteri. Questi profili tecnici usano `DisplayClaims` con un riferimento a DisplayControl.

Per altre informazioni, vedere [Profilo tecnico autosito](restful-technical-profile.md) e [DisplayControl.](display-controls.md)

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Facoltativo] Localizzare il messaggio di posta elettronica

Per localizzare il messaggio di posta elettronica, è necessario inviare le stringhe localizzate a Mail jet o al provider di posta elettronica. Ad esempio, è possibile localizzare l'oggetto, il corpo, il messaggio di codice o la firma del messaggio di posta elettronica. A tale scopo, è possibile usare la trasformazione delle attestazioni [GetLocalizedStringsTransformation](string-transformations.md) per copiare le stringhe localizzate in tipi di attestazione. La `GenerateEmailRequestBody` trasformazione delle attestazioni, che genera il payload JSON, usa attestazioni di input che contengono le stringhe localizzate.

1. Nei criteri definire le attestazioni stringa seguenti: subject, message, codeIntro e signature.
1. Definire una [trasformazione di attestazioni GetLocalizedStringsTransformation](string-transformations.md) per sostituire i valori stringa localizzati nelle attestazioni del passaggio 1.
1. Modificare la trasformazione `GenerateEmailRequestBody` delle attestazioni in modo da usare le attestazioni di input con il frammento XML seguente.
1. Aggiornare il modello Mailsystem in modo che usi parametri dinamici al posto di tutte le stringhe che verranno localizzate da Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Aggiungere [l'elemento Localization](localization.md) seguente.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. Aggiungere riferimenti agli elementi LocalizedResources aggiornando [l'elemento ContentDefinitions.](contentdefinitions.md)

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Aggiungere infine la trasformazione delle attestazioni di input seguente ai `LocalAccountSignUpWithLogonEmail` profili `LocalAccountDiscoveryUsingEmailAddress` tecnici e .

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Facoltativo] Localizzare l'interfaccia utente

L'elemento Localizzazione consente di supportare più impostazioni locali o lingue nei criteri per i percorsi utente. Il supporto della localizzazione nei criteri consente di fornire stringhe specifiche della lingua sia per gli elementi dell'interfaccia utente del controllo visualizzazione verifica [sia](localization-string-ids.md#verification-display-control-user-interface-elements)per i messaggi di errore una sola volta relativi [alla password.](localization-string-ids.md#one-time-password-error-messages) Aggiungere l'elemento LocalizedString seguente a LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Dopo aver aggiunto le stringhe localizzate, rimuovere i metadati dei messaggi di errore di convalida OTP dai profili tecnici LocalAccountSignUpWithLogonEmail e LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare un esempio di criteri di verifica tramite posta elettronica personalizzati in GitHub:

- [Verifica tramite posta elettronica personalizzata - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Per informazioni sull'uso di un'API REST personalizzata o di qualsiasi provider di posta elettronica SMTP basato su HTTP, vedere Definire un profilo tecnico [RESTful in](restful-technical-profile.md)un Azure AD B2C criteri personalizzati.

::: zone-end
