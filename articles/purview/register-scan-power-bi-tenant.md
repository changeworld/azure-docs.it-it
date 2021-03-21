---
title: Registrare e analizzare un tenant di Power BI (anteprima)
description: Informazioni su come usare il portale di Azure per la registrazione e l'analisi di un tenant di Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695746"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrare e analizzare un tenant di Power BI (anteprima)

Questo articolo illustra come usare il portale di Azure per la registrazione e l'analisi di un tenant di Power BI.

> [!Note]
> Se l'istanza di ambito e il tenant di Power BI si trovano nello stesso tenant di Azure, è possibile usare solo l'autenticazione identità gestita (MSI) per configurare un'analisi di un tenant di Power BI. 

## <a name="create-a-security-group-for-permissions"></a>Creare un gruppo di sicurezza per le autorizzazioni

Per configurare l'autenticazione, creare un gruppo di sicurezza e aggiungervi l'identità gestita di competenza.

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure Active Directory**.
1. Creare un nuovo gruppo di sicurezza nel Azure Active Directory, seguendo la procedura [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > È possibile ignorare questo passaggio se si dispone già di un gruppo di sicurezza che si desidera utilizzare.

1. Selezionare **sicurezza** come **tipo di gruppo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo di gruppo di sicurezza":::

1. Aggiungere l'identità gestita di competenza a questo gruppo di sicurezza. Selezionare **membri**, quindi selezionare **+ Aggiungi membri**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Aggiungere l'istanza gestita del catalogo al gruppo.":::

1. Cercare l'identità gestita di competenza e selezionarla.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Aggiungi catalogo eseguendo una ricerca":::

    Verrà visualizzata una notifica di esito positivo che mostra che è stata aggiunta.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Aggiunta del file MSI del catalogo completata":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associare il gruppo di sicurezza al tenant

1. Accedere al [portale di amministrazione Power bi](https://app.powerbi.com/admin-portal/tenantSettings).
1. Selezionare la pagina **Impostazioni tenant** .

    > [!Important]
    > Per visualizzare la pagina delle impostazioni del tenant, è necessario essere un amministratore Power BI.

1. Selezionare **le impostazioni dell'API**  >  **di amministrazione Consenti alle entità servizio di usare Power BI di sola lettura API di amministrazione (anteprima)**.
1. Selezionare **gruppi di sicurezza specifici**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Immagine che illustra come consentire alle entità servizio di ottenere le autorizzazioni di sola lettura Power BI API di amministrazione":::

    > [!Caution]
    > Quando si consente al gruppo di sicurezza creato (che ha l'identità gestita di competenza come membro) di usare le API di amministrazione di Power BI di sola lettura, si consente anche di accedere ai metadati (ad esempio, i nomi di dashboard e report, i proprietari, le descrizioni e così via) per tutti gli elementi di Power BI nel tenant. Una volta che i metadati sono stati inseriti nell'ambito di Azure, le autorizzazioni di competenza, non Power BI autorizzazioni, determinano chi può visualizzare tali metadati.

    > [!Note]
    > È possibile rimuovere il gruppo di sicurezza dalle impostazioni dello sviluppatore, ma i metadati estratti in precedenza non verranno rimossi dall'account di competenza. Se lo si desidera, è possibile eliminarlo separatamente.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrare il Power BI e configurare un'analisi

Ora che sono state concesse le autorizzazioni di gestione delle identità gestite per la connessione all'API di amministrazione del tenant di Power BI, è possibile configurare l'analisi da Azure competenza Studio.

Aggiungere prima di tutto un flag di funzionalità speciale all'URL di competenza 

1. Selezionare l'icona del **centro di gestione** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Icona del centro di gestione.":::

1. Quindi selezionare **+ nuovo** nelle **origini dati**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Immagine del pulsante nuova origine dati":::

    Selezionare **Power bi** come origine dati.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Immagine che mostra l'elenco di origini dati disponibili per la scelta":::

3. Assegnare al Power BI istanza un nome descrittivo.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Immagine che mostra Power BI nome descrittivo dell'origine dati":::

    Il nome deve avere una lunghezza compresa tra 3-63 caratteri e deve contenere solo lettere, numeri, caratteri di sottolineatura e trattini.  Gli spazi non sono consentiti.

    Per impostazione predefinita, il sistema troverà il tenant Power BI esistente nella stessa sottoscrizione di Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI origine dati registrata":::

    > [!Note]
    > Per Power BI, la registrazione e l'analisi dell'origine dati sono consentite solo per un'istanza.


4. Assegnare un nome all'analisi. Selezionare quindi l'opzione per includere o escludere le aree di lavoro personali. Si noti che l'unico metodo di autenticazione supportato è **Identity gestito**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Immagine che mostra la configurazione dell'analisi Power BI":::

    > [!Note]
    > * Il cambio della configurazione di un'analisi per includere o escludere un'area di lavoro personale attiverà un'analisi completa dell'origine Power bi
    > * Il nome dell'analisi deve avere una lunghezza compresa tra 3-63 caratteri e deve contenere solo lettere, numeri, caratteri di sottolineatura e trattini. Gli spazi non sono consentiti.

5. Configurare un trigger di analisi. Le opzioni disponibili sono **una sola volta**, **ogni 7 giorni** e **ogni 30 giorni**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Immagine del trigger Scan":::

6. In **Verifica nuova analisi** selezionare **Salva ed Esegui** per avviare l'analisi.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Salva ed Esegui Power BI immagine della schermata":::

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
