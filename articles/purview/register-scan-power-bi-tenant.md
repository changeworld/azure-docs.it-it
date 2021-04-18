---
title: Registrare ed analizzare un tenant Power BI (anteprima)
description: Informazioni su come usare il portale di Azure Purview per registrare ed analizzare un tenant Power BI tenant.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600863"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrare ed analizzare un tenant Power BI (anteprima)

Questo articolo illustra come usare il portale di Azure Purview per registrare ed analizzare un tenant Power BI tenant.

> [!Note]
> Se l'istanza di Purview e il tenant Power BI sono nello stesso tenant di Azure, è possibile usare solo l'autenticazione dell'identità gestita (MSI) per configurare un'analisi di un tenant Power BI locale. 

## <a name="create-a-security-group-for-permissions"></a>Creare un gruppo di sicurezza per le autorizzazioni

Per configurare l'autenticazione, creare un gruppo di sicurezza e aggiungerne l'identità gestita purview.

1. Nel [portale di Azure](https://portal.azure.com)cercare Azure Active Directory **.**
1. Creare un nuovo gruppo di sicurezza nel Azure Active Directory, seguendo Creare un gruppo di base e [aggiungere membri usando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > È possibile ignorare questo passaggio se si ha già un gruppo di sicurezza che si vuole usare.

1. Selezionare **Sicurezza** come **Tipo di gruppo.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo di gruppo di sicurezza":::

1. Aggiungere l'identità gestita di Purview a questo gruppo di sicurezza. Selezionare **Membri** e quindi **+ Aggiungi membri.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Aggiungere l'istanza gestita del catalogo al gruppo.":::

1. Cercare l'identità gestita di Purview e selezionarla.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Aggiungere un catalogo cercandolo":::

    Verrà visualizzata una notifica di esito positivo che indica che è stata aggiunta.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Aggiunta dell'msi del catalogo riuscita":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associare il gruppo di sicurezza al tenant

1. Accedere al portale [Power BI di amministrazione.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Selezionare la **pagina Impostazioni** tenant.

    > [!Important]
    > È necessario essere un amministratore Power BI per visualizzare la pagina delle impostazioni del tenant.

1. Selezionare **Admin API settings (Impostazioni API** amministratore) Allow service  >  **principals to use read-only Power BI admin APIs (Preview) (Consenti** alle entità servizio di usare le API di sola lettura per gli amministratori - Anteprima).
1. Selezionare **Gruppi di sicurezza specifici.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Immagine che illustra come consentire alle entità servizio di ottenere autorizzazioni di sola lettura per Power BI'API di amministrazione":::

    > [!Caution]
    > Quando si consente al gruppo di sicurezza creato (che ha l'identità gestita Purview come membro) di usare le API di amministrazione di Power BI di sola lettura, si consente anche l'accesso ai metadati (ad esempio nomi di dashboard e report, proprietari, descrizioni e così via) per tutti gli artefatti Power BI in questo tenant. Dopo che i metadati sono stati estratti in Azure Purview, le autorizzazioni di Purview, non Power BI, determinano chi può visualizzare i metadati.

    > [!Note]
    > È possibile rimuovere il gruppo di sicurezza dalle impostazioni dello sviluppatore, ma i metadati estratti in precedenza non verranno rimossi dall'account Purview. Se lo si desidera, è possibile eliminarlo separatamente.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrare Power BI e configurare un'analisi

Dopo aver assegnato le autorizzazioni di Purview Managed Identity per connettersi all'API di amministrazione del tenant di Power BI, è possibile configurare l'analisi da Azure Purview Studio.

1. Selezionare origini **nel** riquadro di spostamento a sinistra.

1. Selezionare **Registra**.

    Selezionare **Power BI** come origine dati.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Immagine che mostra l'elenco delle origini dati disponibili per la scelta":::

3. Assegnare all Power BI istanza un nome descrittivo.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Immagine che mostra Power BI nome descrittivo dell'origine dati":::

    Il nome deve avere una lunghezza compresa tra 3 e 63 caratteri e deve contenere solo lettere, numeri, caratteri di sottolineatura e trattini.  Gli spazi non sono consentiti.

    Per impostazione predefinita, il sistema troverà Power BI tenant esistente nella stessa sottoscrizione di Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI'origine dati registrata":::

    > [!Note]
    > Ad Power BI, la registrazione e l'analisi dell'origine dati sono consentite per una sola istanza.


4. Assegnare un nome all'analisi. Selezionare quindi l'opzione per includere o escludere le aree di lavoro personali. Si noti che l'unico metodo di autenticazione supportato è **Identità gestita**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Immagine che mostra Power BI di analisi":::

    > [!Note]
    > * Se si cambia la configurazione di un'analisi per includere o escludere un'area di lavoro personale, verrà attivata un'analisi completa dell'origine PowerBI
    > * Il nome dell'analisi deve avere una lunghezza compresa tra 3 e 63 caratteri e deve contenere solo lettere, numeri, caratteri di sottolineatura e trattini. Gli spazi non sono consentiti.

5. Configurare un trigger di analisi. Le opzioni sono **Una volta**, **Ogni 7** giorni e **Ogni 30 giorni**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Immagine del trigger di digitalizzazione":::

6. In **Rivedi nuova analisi** selezionare Salva ed esegui **per** avviare l'analisi.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Salvare ed eseguire un'Power BI schermata":::

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
