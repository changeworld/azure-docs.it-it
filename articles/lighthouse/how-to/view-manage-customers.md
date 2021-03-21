---
title: Visualizzare e gestire i clienti e le risorse delegate nel portale di Azure
description: Come provider di servizi o Enterprise che usa Azure Lighthouse, è possibile visualizzare tutte le risorse e le sottoscrizioni delegate visitando i clienti nella portale di Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419330"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Visualizzare e gestire i clienti e le risorse delegate nel portale di Azure

I provider di servizi che usano [Azure Lighthouse](../overview.md) possono usare la pagina **clienti** nella [portale di Azure](https://portal.azure.com) per visualizzare le risorse e le sottoscrizioni delegate del cliente.

> [!TIP]
> Sebbene si faccia riferimento ai provider di servizi e ai clienti, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono usare lo stesso processo per consolidare l'esperienza di gestione.

Per accedere alla pagina **Clienti personali** nel portale di Azure, selezionare **Tutti i servizi**, quindi cercare **Clienti personali** e selezionarla. È possibile trovarla anche immettendo "Clienti personali" nella casella di ricerca nella parte superiore del portale di Azure.

Tenere presente che la sezione **clienti** principali della pagina **clienti personali** Mostra solo le informazioni sui clienti che hanno delegato sottoscrizioni o gruppi di risorse al tenant di Azure Active Directory (Azure ad) tramite Azure Lighthouse. Se si lavora con altri clienti, ad esempio tramite il [programma Cloud Solution Provider (CSP)](/partner-center/csp-overview), non verranno visualizzate informazioni sui clienti nella sezione **Customers** , a meno che non siano state caricate le [risorse in Azure Lighthouse](onboard-customer.md) (anche se è possibile visualizzare i dettagli di alcuni clienti CSP nella [sezione Cloud Solution Provider (anteprima)](#cloud-solution-provider-preview) più in basso nella pagina).

> [!NOTE]
> I clienti possono visualizzare le informazioni sui provider di servizi passando a **Provider di servizi** nel portale di Azure. Per altre informazioni, vedere [Visualizzare e gestire i provider di servizi](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visualizzare e gestire i dettagli dei clienti

Per visualizzare i dettagli dei clienti, selezionare **Clienti** sul lato sinistro della pagina **Clienti personali**.

> [!IMPORTANT]
> Per visualizzare queste informazioni, è necessario che agli utenti sia stato concesso il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso in lettura) al processo di onboarding.

Per ogni cliente, verranno visualizzati il nome del cliente, l'ID del cliente (ID tenant) e l' **ID offerta** e la **versione dell'offerta** associata all'engagement. Nella colonna **Deleghe** saranno visualizzati il numero di sottoscrizioni delegate e/o il numero di gruppi di risorse delegati.

Le opzioni nella parte superiore della pagina consentono di ordinare, filtrare e raggruppare le informazioni dei clienti in base a clienti, offerte o parole chiave specifiche.

Da questa pagina è possibile visualizzare le seguenti informazioni:

- Per visualizzare tutte le sottoscrizioni, le offerte e le deleghe associate a un cliente, selezionare il nome del cliente.
- Per visualizzare altri dettagli su un'offerta e le relative deleghe, selezionare il nome dell'offerta.
- Per visualizzare altri dettagli sulle assegnazioni di ruolo per le sottoscrizioni o i gruppi di risorse delegati, selezionare la voce nella colonna **Deleghe**.

## <a name="view-and-manage-delegations"></a>Visualizzazione e gestione delle deleghe

Le deleghe mostrano la sottoscrizione o il gruppo di risorse che è stato delegato, insieme agli utenti e alle autorizzazioni che possono accedervi. Per visualizzare queste informazioni, selezionare **Deleghe** sul lato sinistro della pagina **Clienti personali**.

Le opzioni nella parte superiore della pagina consentono di ordinare, filtrare e raggruppare queste informazioni in base a clienti, offerte o parole chiave specifiche.

### <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Gli utenti e le autorizzazioni associati a ogni delega vengono visualizzati nella colonna **Assegnazioni di ruoli**. È possibile selezionare ogni voce per visualizzare l'elenco completo di utenti, gruppi ed entità servizio a cui è stato concesso l'accesso alla sottoscrizione o al gruppo di risorse. Da qui è possibile selezionare un utente, un gruppo o un nome dell'entità servizio specifico per ottenere altri dettagli.

### <a name="remove-delegations"></a>Rimuovi delega

Se sono stati inclusi utenti con il [ruolo di eliminazione della registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente in Azure Lighthouse, gli utenti possono rimuovere una delega selezionando l'icona del cestino che viene visualizzata nella riga relativa alla delega. In tal caso, nessun utente nel tenant del provider di servizi sarà in grado di accedere alle risorse precedentemente Delegate.

Per altre informazioni, vedere [rimuovere l'accesso a una delega](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Visualizza l'attività di modifica della delega

La sezione **log attività** della pagina **My customers** tiene traccia di ogni volta che le sottoscrizioni dei clienti o i gruppi di risorse vengono delegati al tenant e ogni volta che le risorse delegate in precedenza vengono rimosse. Queste informazioni possono essere visualizzate solo dagli utenti a cui è stato [assegnato il ruolo di lettore di monitoraggio nell'ambito radice](monitor-delegation-changes.md).

Per ulteriori informazioni, vedere [visualizzare le modifiche di delega nel portale di Azure](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Lavorare nel contesto di una sottoscrizione delegata

È possibile lavorare direttamente nel contesto di una sottoscrizione delegata all'interno del portale di Azure, senza cambiare la directory a cui si è connessi. A tale scopo, procedere nel seguente modo:

1. Selezionare l'icona **Directory e sottoscrizione** nella parte superiore del portale di Azure.
2. Nel **filtro della sottoscrizione predefinita**, assicurarsi che sia selezionata solo la casella relativa alla sottoscrizione delegata. È possibile usare la casella a discesa **Directory corrente e delegate** per mostrare solo le sottoscrizioni in una directory specifica. Non usare l'opzione **Cambia directory** perché cambierebbe la directory a cui è stato eseguito l'accesso.

Se quindi si accede a un servizio che supporta le [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md), il servizio userà per impostazione predefinita il contesto della sottoscrizione delegata selezionata. Per modificare questa impostazione, seguire i passaggi precedenti e selezionare la casella **Seleziona tutto** (o scegliere una o più sottoscrizioni da usare in alternativa).

> [!NOTE]
> Se è stato concesso l'accesso a uno o più gruppi di risorse, anziché accedere a un'intera sottoscrizione, selezionare la sottoscrizione a cui appartiene il gruppo di risorse. Sarà quindi possibile lavorare nel contesto di tale sottoscrizione, ma si potrà accedere solo ai gruppi di risorse designati.

È anche possibile accedere alle funzionalità correlate a sottoscrizioni o gruppi di risorse delegati dall'interno di servizi che supportano esperienze di gestione tra tenant selezionando la sottoscrizione o il gruppo di risorse da tale servizio.

## <a name="cloud-solution-provider-preview"></a>Cloud Solution Provider (anteprima)

Una sezione del **provider di soluzioni cloud (anteprima)** separata della pagina **clienti** Mostra le informazioni e le risorse di fatturazione per i clienti CSP che hanno [firmato il contratto di servizio Microsoft (MCA)](/partner-center/confirm-customer-agreement) e si trovano nel [piano Azure](/partner-center/azure-plan-get-started). Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione all'account di fatturazione Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).

Questi clienti CSP verranno visualizzati in questa sezione anche se sono stati caricati in Azure Lighthouse. Analogamente, non è necessario che un cliente CSP venga visualizzato nella sezione del **provider di soluzioni cloud (anteprima)** dei **clienti** per caricarli nel Faro di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- Informazioni su come i clienti possono [visualizzare e gestire i provider di servizi](view-manage-service-providers.md) passando a **Provider di servizi** nel portale di Azure.
