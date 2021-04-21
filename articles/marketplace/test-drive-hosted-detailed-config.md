---
title: Configurazione dettagliata per un'test drive ospitata in Azure Marketplace
description: Procedura di configurazione della spiegazione per un test drive ospitato nel marketplace commerciale
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812418"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Configurazione dettagliata per i test drive ospitati

Questo articolo descrive come configurare un servizio ospitato test drive Dynamics 365 for Customer Engagement o Dynamics 365 for Operations.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configurare per Dynamics 365 Customer Engagement

1. Accedere al [Centro per i partner](https://partner.microsoft.com/).
2. Se non è possibile accedere al collegamento precedente, è necessario inviare una richiesta [qui](https://appsource.microsoft.com/partners/list-an-app) per pubblicare l'applicazione. Dopo aver esaminato la richiesta, verrà concesso l'accesso per avviare il processo di pubblicazione.
3. Trovare **un'offerta Dynamics 365 for Customer Engagement** esistente o creare una nuova offerta Dynamics **365 for Customer** Engagement.
4. Selezionare la **casella di controllo Enable a test drive** (Abilita un test drive) e selezionare un tipo **di** test drive (vedere il punto elenco seguente), quindi selezionare **Save (Salva).**

    [![Selezionare la casella di controllo "Enable a test drive" (Abilita un test drive).](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Tipo di test drive:** scegliere **Microsoft Hosted (Dynamics 365 for Customer Engagement & PowerApps).** Ciò indica che Microsoft ospiterà e manterrà il servizio che esegue il provisioning e il deprovisioning test drive utenti.

5. Concedere Microsoft AppSource per effettuare il provisioning e il deprovisioning test drive utenti nel tenant usando [queste istruzioni.](./test-drive-azure-subscription-setup.md) In questo passaggio verranno generati **l'ID** App Azure AD e App Azure AD **chiave** specificati di seguito.
6. Completare questi campi nella **pagina configurazione tecnica del test** drive.

    [![Pagina test drive configurazione tecnica.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Numero massimo di test drive** simultanei: numero di utenti simultanei che possono avere un test drive in esecuzione contemporaneamente. Ogni utente usa una licenza dynamics mentre il test drive è attivo, quindi assicurarsi di avere almeno queste licenze dynamics disponibili per gli test drive utenti. Sono consigliati da 3 a 5.
    - **Durata del test drive:** numero di ore in cui il test drive dell'utente sarà attivo. Dopo la scadenza del periodo di tempo, l'utente verrà deprovisioning dal tenant. È consigliabile 2-24 ore a seconda della complessità dell'app. L'utente può sempre richiedere un altro test drive se si eserciterà il tempo e vorrà accedere di nuovo al test drive.
    - **URL dell'istanza:** URL a cui test drive'utente verrà inviato all'avvio del test drive. Questo è in genere l'URL dell'istanza di Dynamics 365 in cui sono installati l'app e i dati di esempio. Valore di esempio: `https://testdrive.crm.dynamics.com` .
    - **URL API Web dell'istanza:** URL dell'API Web per l'istanza di Dynamics 365. Recuperare questo valore accedendo all'istanza di Microsoft Dynamics 365 e passando all'API Web **Setting** Customization Developer Resources Instance e  >    >    >   copiando l'indirizzo (URL). Valore di esempio

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Esempio di API Web dell'istanza.":::

    - **Nome ruolo:** nome del ruolo di sicurezza dynamics 365 personalizzato creato per test drive oppure è possibile usare un ruolo esistente. Un nuovo ruolo deve avere privilegi minimi necessari aggiunti al ruolo per accedere a un'istanza di Customer Engagement. Fare riferimento [a Privilegi minimi necessari per accedere a Microsoft Dynamics 365.](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365) Questo è il ruolo che verrà assegnato agli utenti durante l'test drive. Valore di esempio: `testdriverole` .
    
        > [!IMPORTANT]
        > Assicurarsi che il controllo del gruppo di sicurezza non sia stato aggiunto. In questo modo l'utente può essere sincronizzato con l'istanza di Customer Engagement.

    - **Azure Active Directory ID tenant:** ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere a portale di Azure e passare a Azure Active Directory  >  **Proprietà** e copiare l'ID directory. Valore di esempio: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory tenant:** nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato `<tenantname>.onmicrosoft.com`. Valore di esempio: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID applicazione:** ID dell'app Azure Active Directory (AD) creata nel passaggio 5. Valore di esempio: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segreto client dell'applicazione:** segreto per l Azure AD app creata nel passaggio 5. Valore di esempio: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Specificare i dettagli dell'inserzione nel Marketplace. Selezionare **Lingua** per visualizzare altri campi obbligatori.

    [![Pagina dei dettagli dell'inserzione nel Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrizione:** panoramica dell'test drive. Questo testo verrà visualizzato all'utente durante il test drive il provisioning. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
    - **Manuale dell'utente:** manuale dell'utente pdf che test drive agli utenti di comprendere come usare l'app.
    - **Video dimostrativo del test drive:** video che illustra l'app (facoltativo).

## <a name="configure-for-dynamics-365-operations"></a>Configurare per Dynamics 365 Operations

2. Se non è possibile accedere al collegamento precedente, è necessario inviare una richiesta [qui](https://appsource.microsoft.com/partners/list-an-app) per pubblicare l'applicazione. Dopo aver esaminato la richiesta, verrà concesso l'accesso per avviare il processo di pubblicazione.
3. Trovare **un'offerta Dynamics 365 for Operations** esistente o creare una nuova offerta Dynamics **365 for Operations.**
4. Selezionare la **casella di controllo Enable a test drive** (Abilita un test drive) e selezionare un tipo **di** test drive (vedere il punto elenco seguente), quindi selezionare **Save (Salva).**

    [![Selezionare la casella di controllo "Abilita test drive".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Tipo di test drive:** scegliere **l'opzione Dynamics 365 for Operations.** Ciò significa che Microsoft ospiterà e manterrà il servizio che esegue il provisioning e il deprovisioning test drive utenti.

5. Concedere Microsoft AppSource autorizzazioni per effettuare il provisioning e il deprovisioning test drive utenti nel tenant usando [queste istruzioni.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) In questo passaggio verranno generati **l'ID** App Azure AD e App Azure AD **chiave** indicati di seguito.
6. Completare questi campi nella pagina **di configurazione tecnica del test** drive.

    [![Pagina di configurazione tecnica del Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Numero massimo di test drive** simultanei: numero di utenti simultanei che possono avere un test drive in esecuzione contemporaneamente. Ogni utente usa una licenza Dynamics mentre il test drive è attivo, quindi assicurarsi di avere almeno queste licenze dynamics disponibili per gli test drive utenti. È consigliabile da 3 a 5.
    - **Durata test drive:** numero di ore in cui il test drive dell'utente sarà attivo. Dopo la scadenza del periodo di tempo, l'utente verrà deprovisioning dal tenant. È consigliabile 2-24 ore a seconda della complessità dell'app. L'utente può sempre richiedere un altro test drive se si eserciterà il tempo e vorrà accedere di nuovo al test drive.
    - **URL dell'istanza:** URL a cui test drive'utente verrà inviato all'avvio del test drive. Questo è in genere l'URL dell'istanza di Dynamics 365 in cui sono installati l'app e i dati di esempio. Valore di esempio: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory ID tenant:** ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere a portale di Azure e passare a Azure Active Directory  >  **Proprietà** e copiare l'ID directory. Valore di esempio: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory tenant:** nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato `<tenantname>.onmicrosoft.com`. Valore di esempio: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID applicazione:** ID dell'app Azure Active Directory (AD) creata nel passaggio 5. Valore di esempio: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segreto client dell'applicazione:** segreto per l Azure AD app creata nel passaggio 5. Valore di esempio: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Entità legale della versione di** valutazione: fornire una persona legale per assegnare un utente della versione di valutazione. È possibile crearne uno nuovo in [Creare o modificare una persona legale.](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)
    - **Nome ruolo:** nome AOT (Albero oggetti applicazione) del ruolo di sicurezza di Dynamics 365 personalizzato creato per test drive. Questo è il ruolo che verrà assegnato agli utenti durante l'test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Pagina di configurazione della sicurezza.":::

7. Specificare i dettagli dell'inserzione nel Marketplace. Selezionare **Lingua** per visualizzare altri campi obbligatori.

    [![Pagina dei dettagli dell'inserzione nel Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrizione:** panoramica dell'test drive. Questo testo verrà visualizzato all'utente durante il test drive il provisioning. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
    - **Manuale dell'utente:** manuale dell'utente pdf che test drive agli utenti di comprendere come usare l'app.
    - **Video dimostrativo del test drive:** video che illustra l'app (facoltativo).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->