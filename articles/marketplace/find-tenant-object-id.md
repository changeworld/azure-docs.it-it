---
title: Trovare i dettagli relativi a ID tenant, ID oggetto e associazione partner in Azure Marketplace
description: Come trovare l'ID tenant, l'ID oggetto e i dettagli dell'associazione partner di un ID sottoscrizione in Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109343"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Trovare l'ID tenant, l'ID oggetto e i dettagli dell'associazione partner

Questo articolo descrive come trovare l'ID tenant, l'ID oggetto e i dettagli dell'associazione partner, insieme ai rispettivi ID sottoscrizione.

Se è necessario ottenere screenshot di questi elementi in Azure Cloud Shell da usare per l'assistenza al debug, passare a [trovare l'associazione di tenant, oggetti e ID partner per il debug](#find-ids-for-debugging).

>[!Note]
> Solo il proprietario di una sottoscrizione dispone dei privilegi necessari per eseguire questi passaggi.

## <a name="find-tenant-id"></a>Trova ID tenant

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Selezionare **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Icona Azure Active Directory in portale di Azure.":::

3. Selezionare la scheda di **informazioni generali**. L'ID tenant dovrebbe essere visualizzato in **informazioni di base**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Selezionare i gruppi in portale di Azure.":::

## <a name="find-subscriptions-and-roles"></a>Trova sottoscrizioni e ruoli

1. Passare alla portale di Azure e selezionare **Azure Active Directory** come indicato nei passaggi 1 e 2 precedenti.
2. Selezionare **Sottoscrizioni**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Icona delle sottoscrizioni in portale di Azure.":::

3. Visualizzare sottoscrizioni e ruoli.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Schermata sottoscrizioni in portale di Azure.":::

## <a name="find-partner-id"></a>Trova ID partner

1. Passare alla pagina sottoscrizioni come descritto nella sezione precedente.
2. Selezionare una sottoscrizione.
3. In **fatturazione** selezionare **informazioni partner**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Informazioni sui partner nel menu di spostamento a sinistra.":::

## <a name="find-user-object-id"></a>Trova utente (ID oggetto)

1. Accedere al portale di [amministrazione di Office 365](https://portal.office.com/adminportal/home) con un account nel tenant desiderato con i diritti amministrativi appropriati.
2. Nel menu a sinistra espandere la sezione centri di **Amministrazione** nella parte inferiore e quindi selezionare l'opzione Azure Active Directory per avviare la console di amministrazione in una nuova finestra del browser.
3. Selezionare **Utenti**.
4. Individuare o cercare l'utente desiderato, quindi selezionare il nome dell'account per visualizzare le informazioni sul profilo dell'account utente.
5. L'ID oggetto si trova nella sezione Identity a destra.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Centro di amministrazione Azure Active Directory.":::

6. Individuare le **assegnazioni di ruolo** selezionando **controllo di accesso (IAM)** nel menu a sinistra, quindi **assegnazioni di ruolo**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Assegnazioni di ruolo per le risorse di Azure.":::

## <a name="find-ids-for-debugging"></a>Trova ID per il debug

Questa sezione descrive come trovare l'associazione di tenant, oggetti e ID partner a scopo di debug.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Aprire Azure Cloud Shell selezionando l'icona di PowerShell in alto a destra.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Icona di PowerShell nella parte superiore destra della schermata.":::

3. Selezionare **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Selezionare il collegamento di PowerShell.":::

4. Selezionare la casella **sottoscrizione** per scegliere quella a cui è collegato il partner, quindi **creare l'archiviazione**. Questa operazione può essere eseguita una sola volta. Se è già stata configurata l'archiviazione, procedere al passaggio successivo.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Selezionare il pulsante Crea archiviazione.":::

5. Quando si crea o si dispone già di spazio di archiviazione, viene visualizzata la finestra Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Finestra Azure Cloud Shell.":::

6. Per i dettagli dell'associazione partner, installare l'estensione con questo comando:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell noterà se l'estensione è già installata:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="La finestra Azure Cloud Shell che mostra l'estensione è già installata.":::

7. Controllare i dettagli del partner usando questo comando:<br>`az managementpartner show --partner-id xxxxxx`<br>Esempio: `az managementpartner show --partner-id 4760962`.<br>Blocca uno screenshot dei risultati del comando, che sarà simile al seguente:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Schermata di esempio che mostra i risultati del comando precedente per visualizzare l'ID del componente.":::

>[!NOTE]
>Se più sottoscrizioni richiedono una schermata, utilizzare questo comando per passare da una sottoscrizione all'altra:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Passaggi successivi

- [Paesi e aree supportati](sell-from-countries.md)