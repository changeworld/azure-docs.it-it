---
title: Personalizzare l'interfaccia utente
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'interfaccia utente per le applicazioni che usano Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055862"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personalizzazione e la personalizzazione dell'interfaccia utente che Azure Active Directory B2C (Azure AD B2C) Visualizza ai clienti, contribuisce a offrire un'esperienza utente uniforme nell'applicazione. Queste esperienze includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. Questo articolo illustra come personalizzare le pagine di Azure AD B2C usando il modello di pagina e le informazioni personalizzate distintive dell'azienda. 

> [!TIP]
> Per personalizzare altri aspetti delle pagine del flusso utente oltre il modello di pagina, il logo del banner, l'immagine di sfondo o il colore di sfondo, vedere How to [Customize the UI with HTML template](customize-ui-with-html.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Panoramica

Azure AD B2C forniscono diversi modelli predefiniti che è possibile scegliere per offrire alle pagine dell'esperienza utente un aspetto professionale. Questi modelli di pagina possono anche essere usati come punto di partenza per la personalizzazione, usando la funzionalità di personalizzazione della [società](#company-branding) .

### <a name="ocean-blue"></a>Blu oceano

Esempio di modello blu oceano di cui è stato eseguito il rendering nella pagina di accesso per l'iscrizione:

![Schermata Modello blu oceano](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Grigio ardesia

Esempio del modello grigio dello Slate visualizzato nella pagina di accesso all'iscrizione:

![Schermata del modello grigio dello Slate](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Classic

Esempio del modello classico di cui è stato eseguito il rendering nella pagina di accesso all'iscrizione:

![Schermata modello classico](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Informazioni personalizzate distintive dell'azienda

È possibile personalizzare le pagine di Azure AD B2C con un logo banner, un'immagine di sfondo e un colore di sfondo utilizzando Azure Active Directory informazioni personalizzate distintive dell' [azienda](../active-directory/fundamentals/customize-branding.md). Le informazioni personalizzate distintive dell'azienda includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. 

Nell'esempio seguente viene illustrata una pagina di iscrizione *e accesso* con un logo personalizzato, un'immagine di sfondo, usando il modello Blue Ocean:

![Pagina di iscrizione/accesso personalizzata fornita da Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Selezionare un modello di pagina

::: zone pivot="b2c-user-flow"

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare un flusso utente che si desidera personalizzare.
1. In **Personalizza** nel menu a sinistra selezionare **layout di pagina** e quindi selezionare un **modello**.
    ![Elenco a discesa Selezione modello nella pagina flusso utente di portale di Azure](./media/customize-ui/select-page-template.png)

Quando si sceglie un modello, il modello selezionato viene applicato a tutte le pagine del flusso utente. L'URI per ogni pagina è visibile nel campo **URI pagina personalizzata** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Per selezionare un modello di pagina, impostare l' `LoadUri` elemento delle [definizioni di contenuto](contentdefinitions.md). Nell'esempio seguente vengono illustrati gli identificatori delle definizioni di contenuto e l'oggetto corrispondente `LoadUri` . 

Blu oceano:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Grigio ardesia:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Classico 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Configurare la personalizzazione aziendale

Per personalizzare le pagine del flusso utente, è necessario prima configurare le informazioni personalizzate distintive dell'azienda in Azure Active Directory, quindi abilitarla nei flussi utente Azure AD B2C.

Per iniziare, impostare il logo del banner, l'immagine di sfondo e il colore di sfondo all'interno della **personalizzazione dell'azienda**.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare informazioni personalizzate distintive dell' **azienda**.
1. Seguire la procedura descritta in [aggiungere informazioni personalizzate alla pagina di accesso dell'Azure Active Directory dell'organizzazione](../active-directory/fundamentals/customize-branding.md).

Tenere presente queste considerazioni quando si configura la personalizzazione dell'azienda in Azure AD B2C:

* Il marchio dell'azienda in Azure AD B2C è attualmente limitato all' **immagine di sfondo**, al **logo banner** e alla personalizzazione del **colore di sfondo** . Le altre proprietà del riquadro branding aziendale, ad esempio **Impostazioni avanzate**, *non sono supportate*.
* Nelle pagine del flusso utente, il colore di sfondo viene visualizzato prima del caricamento dell'immagine di sfondo. È consigliabile scegliere un colore di sfondo che corrisponda strettamente ai colori nell'immagine di sfondo per un'esperienza di caricamento più uniforme.
* Il logo del banner viene visualizzato nei messaggi di posta elettronica di verifica inviati agli utenti quando avviano un flusso utente di iscrizione.



## <a name="enable-company-branding-in-user-flow-pages"></a>Abilitare le informazioni personalizzate distintive dell'azienda nelle pagine flusso utente

::: zone pivot="b2c-user-flow"

Una volta configurata la personalizzazione dell'azienda, abilitarla nei flussi utente.

1. Nel menu a sinistra del portale di Azure selezionare **Azure ad B2C**.
1. In **Criteri** selezionare **Flussi utente (criteri)** .
1. Selezionare il flusso utente per il quale si desidera abilitare la personalizzazione dell'azienda. Le informazioni personalizzate distintive dell'azienda **non sono supportate** per i tipi di flusso utente standard per l' *accesso* e la *modifica del profilo* standard.
1. In **Personalizza** selezionare **layout di pagina**, quindi selezionare la pagina che si vuole personalizzare. Ad esempio, selezionare la **pagina di iscrizione o di accesso unificata**.
1. Per la **versione del layout di pagina (anteprima)**, scegliere la versione **1.2.0** o successiva.
1. Selezionare **Salva**.

Se si vuole personalizzare tutte le pagine del flusso utente, impostare la versione del layout di pagina per ogni layout di pagina nel flusso utente.

![Selezione del layout di pagina in Azure AD B2C nel portale di Azure](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Una volta configurata la personalizzazione dell'azienda, abilitarla nel criterio personalizzato. Configurare la [versione del layout di pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina `contract` per *tutte* le definizioni di contenuto nel criterio personalizzato. Il formato del valore deve contenere la parola `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**Contract**:p Age-Name: Version_. Per specificare un layout di pagina nei criteri personalizzati che utilizzano un valore **DataUri** precedente. Per ulteriori informazioni, vedere come [eseguire la migrazione al layout di pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina.

Nell'esempio seguente vengono illustrate le definizioni di contenuto con il relativo contratto di pagina e il modello di pagina *blu oceano* : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come personalizzare l'interfaccia utente delle applicazioni, vedere [personalizzare l'interfaccia utente dell'applicazione in Azure Active Directory B2C](customize-ui-with-html.md).
