---
title: Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare i minori che utilizzano l'applicazione.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522433"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Il controllo Age in Azure Active Directory B2C (Azure AD B2C) consente di identificare i minorenni che vogliono usare l'applicazione, con o senza il consenso dei genitori. È possibile scegliere di bloccare il minore dall'accesso nell'applicazione. In alternativa, consentire l'uso di per completare l'accesso e fornire all'applicazione lo stato secondario. 

>[!IMPORTANT]
>Questa funzionalità è disponibile in anteprima pubblica. Non usarla per le applicazioni di produzione.
>

Quando il controllo di età è abilitato per un flusso utente, agli utenti viene richiesto di effettuare la data di nascita e il paese di residenza. Se un utente esegue l'accesso senza avere precedentemente immesso queste informazioni, dovrà farlo al successivo accesso. Le regole vengono applicate ogni volta che un utente esegue l'accesso.

![Screenshot delle informazioni sul controllo dell'età raccolta dei flussi](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C usa le informazioni immesse dall'utente per stabilire se si tratta di un minore. Il campo **ageGroup** viene aggiornato di conseguenza nel rispettivo account. Il valore può essere `null`, `Undefined`, `Minor`, `Adult` e `NotAdult`.  I campi **ageGroup** e **consentProvidedForMinor** vengono quindi usati per calcolare il valore di **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Configurare il tenant per il controllo dell'accesso in base all'età

Per usare il controllo Age in un flusso utente, è necessario configurare il tenant in modo che disponga di proprietà aggiuntive.

1. Assicurarsi di usare la directory che contiene il tenant Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto. Selezionare la directory contenente il tenant.
1. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Proprietà** per il tenant nel menu a sinistra.
1. Sotto il controllo **Age** selezionare **Configure (Configura**).
1. Attendere il completamento dell'operazione e il tenant verrà configurato per il controllo dell'accesso in base all'età.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Abilitare il controllo dell'accesso in base all'età nel flusso utente

Dopo che il tenant è stato configurato per l'uso del controllo dell'accesso in base all'età, è possibile usare questa funzionalità nei [flussi utente](user-flow-versions.md) in cui è abilitata. Abilitare il controllo dell'accesso in base all'età tramite i passaggi seguenti:

1. Creare un flusso utente con il controllo dell'accesso in base all'età abilitato.
1. Dopo aver creato il flusso utente, selezionare **Proprietà** nel menu.
1. Nella sezione **Controllo dell'accesso in base all'età** selezionare **Abilitato**.
1. Per l' **iscrizione o l'accesso**, selezionare il modo in cui si desidera gestire gli utenti:
    - Consentire ai minori di accedere all'applicazione.
    - Blocca solo i minorenni al di sotto dell'età del consenso per l'accesso all'applicazione.
    - Impedisce a tutti i minorenni di accedere all'applicazione.
1. Per **in blocco**, selezionare una delle opzioni seguenti:
    - **Inviare un file JSON all'applicazione** : questa opzione Invia una risposta all'applicazione che è stata bloccata da un minore.
    - **Mostra una pagina di errore** : all'utente viene visualizzata una pagina che informa che non è possibile accedere all'applicazione.

## <a name="test-your-user-flow"></a>Testare il flusso utente

1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui flusso utente** .
1. Accedere con un account locale o di social networking. Selezionare quindi il paese di residenza e la data di nascita che simulano una minore. 
1. Ripetere il test e selezionare una data di nascita che simula un adulto.  

Quando si accede come un'entità secondaria, viene visualizzato il messaggio di errore seguente: *Sfortunatamente, l'accesso è stato bloccato. Le leggi sulla privacy e sulla sicurezza in linea nel proprio paese impediscono l'accesso agli account appartenenti agli elementi figlio.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Abilitare il controllo dell'età nei criteri personalizzati

1. Ottenere l'esempio di criteri di controllo delle età su [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure ad B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .
1. Caricare i file dei criteri.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire l'accesso degli utenti in Azure ad B2C](manage-user-access.md).

