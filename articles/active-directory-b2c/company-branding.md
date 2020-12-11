---
title: Aggiungere la personalizzazione alla pagina di accesso dell'organizzazione
titleSuffix: Azure AD B2C
description: Informazioni su come aggiungere la personalizzazione dell'organizzazione alle pagine Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111303"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Aggiungere la personalizzazione alle pagine di Azure Active Directory B2C dell'organizzazione

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

È possibile personalizzare le pagine di Azure AD B2C con un logo banner, un'immagine di sfondo e un colore di sfondo utilizzando Azure Active Directory informazioni personalizzate distintive dell' [azienda](../active-directory/fundamentals/customize-branding.md). Le informazioni personalizzate distintive dell'azienda includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. 

> [!TIP]
> Per personalizzare altri aspetti delle pagine del flusso utente oltre il logo del banner, l'immagine di sfondo o il colore di sfondo, vedere How to [Customize the UI with HTML template](customize-ui-with-html.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Per personalizzare le pagine del flusso utente, è necessario prima configurare le informazioni personalizzate distintive dell'azienda in Azure Active Directory, quindi abilitarla nei layout di pagina dei flussi utente Azure AD B2C.

## <a name="configure-company-branding"></a>Configurare la personalizzazione aziendale

Per iniziare, impostare il logo del banner, l'immagine di sfondo e il colore di sfondo all'interno della **personalizzazione dell'azienda**.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare informazioni personalizzate distintive dell' **azienda**.
1. Seguire la procedura descritta in [aggiungere informazioni personalizzate alla pagina di accesso dell'Azure Active Directory dell'organizzazione](../active-directory/fundamentals/customize-branding.md).

Tenere presente queste considerazioni quando si configura la personalizzazione dell'azienda in Azure AD B2C:

* Il marchio dell'azienda in Azure AD B2C è attualmente limitato all' **immagine di sfondo**, al **logo banner** e alla personalizzazione del **colore di sfondo** . Le altre proprietà del riquadro branding aziendale, ad esempio quelle in **Impostazioni avanzate**, non sono *supportate*.
* Nelle pagine del flusso utente, il colore di sfondo viene visualizzato prima del caricamento dell'immagine di sfondo. È consigliabile scegliere un colore di sfondo che corrisponda strettamente ai colori nell'immagine di sfondo per un'esperienza di caricamento più uniforme.
* Il logo del banner viene visualizzato nei messaggi di posta elettronica di verifica inviati agli utenti quando avviano un flusso utente di iscrizione.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Abilitare la personalizzazione nelle pagine flusso utente

Una volta configurata la personalizzazione dell'azienda, abilitarla nei flussi utente.

1. Nel menu a sinistra del portale di Azure selezionare **Azure ad B2C**.
1. In **Criteri** selezionare **Flussi utente (criteri)** .
1. Selezionare il flusso utente per il quale si desidera abilitare la personalizzazione dell'azienda. Le informazioni personalizzate distintive dell'azienda **non sono supportate** per i tipi di flusso utente standard per l' *accesso* e la *modifica del profilo* standard.
1. In **Personalizza** selezionare **layout di pagina** e quindi selezionare il layout che si desidera personalizzare. Ad esempio, selezionare la **pagina di iscrizione o di accesso unificata**.
1. Per la **versione del layout di pagina (anteprima)**, scegliere la versione **1.2.0** o successiva.
1. Selezionare **Salva**.

Se si vuole personalizzare tutte le pagine del flusso utente, impostare la versione del layout di pagina per ogni layout di pagina nel flusso utente.

![Selezione del layout di pagina in Azure AD B2C nel portale di Azure](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Selezionare un layout di pagina

Per abilitare la personalizzazione dell'azienda, è necessario [definire una versione del layout di pagina](contentdefinitions.md#migrating-to-page-layout) con `contract` la versione della pagina per *tutte* le definizioni di contenuto nel criterio personalizzato. Il formato del valore deve contenere la parola `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**Contract**:p Age-Name: Version_. Per specificare un layout di pagina nei criteri personalizzati che utilizzano un valore **DataUri** precedente.

Informazioni su come eseguire la [migrazione al layout di pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina.

Nell'esempio seguente vengono illustrati gli identificatori delle definizioni di contenuto e il corrispondente **DataUri** con il contratto di pagina: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

L'esempio seguente mostra un logo banner personalizzato e un'immagine di sfondo in una pagina di *iscrizione e accesso* del flusso utente che usa il modello Blue Ocean:

![Pagina di iscrizione/accesso personalizzata fornita da Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Usare asset di personalizzazione dell'azienda in codice HTML personalizzato

Per usare le risorse di personalizzazione della società in [codice HTML personalizzato](customize-ui-with-html.md), aggiungere i tag seguenti al di fuori del `<div id="api">` Tag:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

L'origine dell'immagine viene sostituita con quella dell'immagine di sfondo e il logo del banner.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come personalizzare l'interfaccia utente delle applicazioni, vedere [personalizzare l'interfaccia utente dell'applicazione in Azure Active Directory B2C](customize-ui-with-html.md).