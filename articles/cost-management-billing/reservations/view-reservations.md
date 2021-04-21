---
title: Autorizzazioni per visualizzare e gestire le prenotazioni di Azure
description: Informazioni su come visualizzare e gestire le prenotazioni di Azure nel portale di Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780462"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Autorizzazioni per visualizzare e gestire le prenotazioni di Azure

Questo articolo illustra il funzionamento delle autorizzazioni di prenotazione e come gli utenti possono visualizzare e gestire le prenotazioni di Azure portale di Azure.

## <a name="who-can-manage-a-reservation-by-default"></a>Chi può gestire una prenotazione per impostazione predefinita

Per impostazione predefinita, le prenotazioni possono essere visualizzate e gestite dagli utenti seguenti:

- L'utente che acquista una prenotazione e l'amministratore account della sottoscrizione di fatturazione usata per acquistare la prenotazione vengono aggiunti all'ordine di prenotazione.
- Amministratori fatturazione con Contratto Enterprise e Contratto del cliente Microsoft.
- Utenti con accesso con privilegi elevati per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure

Il ciclo di vita della prenotazione è indipendente da una sottoscrizione di Azure, quindi la prenotazione non è una risorsa nella sottoscrizione di Azure. Si tratta invece di una risorsa a livello di tenant con la propria autorizzazione controllo degli accessi in base al ruolo di Azure separata dalle sottoscrizioni. Le prenotazioni non ereditano le autorizzazioni dalle sottoscrizioni dopo l'acquisto.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Come gli amministratori della fatturazione possono visualizzare o gestire le prenotazioni

Se si è un amministratore della fatturazione, seguire questa procedura per visualizzare e gestire tutte le prenotazioni e le transazioni di prenotazione.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Gestione costi e fatturazione.**
    - Se si è un amministratore EA, nel  menu a sinistra selezionare Ambiti di fatturazione e quindi nell'elenco degli ambiti di fatturazione selezionarne uno.
    - Se si è un proprietario Contratto del cliente Microsoft profilo di fatturazione, nel menu a sinistra selezionare **Profili di fatturazione**. Nell'elenco dei profili di fatturazione selezionarne uno.
1. Nel menu a sinistra selezionare **Prodotti e servizi**  >  **Prenotazioni**.
1. Viene visualizzato l'elenco completo delle prenotazioni per la registrazione EA o il profilo di fatturazione.
1. Gli amministratori della fatturazione possono assumere la proprietà di una prenotazione selezionandola e quindi selezionando **Concedi** accesso nella finestra visualizzata.

### <a name="how-to-add-billing-administrators"></a>Come aggiungere amministratori della fatturazione

Aggiungere un utente come amministratore della fatturazione a un Contratto Enterprise o a un Contratto del cliente Microsoft:

- Per un Contratto Enterprise, aggiungere gli utenti con il ruolo di _Amministratore dell'organizzazione_ per visualizzare e gestire tutti gli ordini di prenotazione applicabili al Contratto Enterprise. Gli amministratori aziendali possono visualizzare e gestire le prenotazioni in **Gestione costi e fatturazione.**
    - Gli utenti con _il ruolo Amministratore aziendale (sola lettura)_ possono visualizzare la prenotazione solo da Gestione costi e **fatturazione.** 
    - Gli amministratori del reparto e i proprietari dell'account non possono visualizzare le prenotazioni _a meno che_ non vengano aggiunti ad esse in modo esplicito tramite Controllo di accesso (IAM). Per altre informazioni, vedere [Gestione dei ruoli Enterprise di Azure](../manage/understand-ea-roles.md).
- Per un Contratto del cliente Microsoft, gli utenti con il ruolo di proprietario del profilo di fatturazione o di collaboratore per il profilo di fatturazione possono gestire tutti gli acquisti di prenotazioni effettuati usando il profilo di fatturazione. Gli utenti con il ruolo di lettore profilo di fatturazione e responsabile fatturazione possono visualizzare tutte le prenotazioni pagate con il profilo di fatturazione. Non possono però apportare modifiche alle prenotazioni.
    Per altre informazioni, vedere [Ruoli e attività del profilo di fatturazione](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Visualizzare le prenotazioni con l'accesso RBAC di Azure

Se la prenotazione è stata acquistata o si viene aggiunti a una prenotazione, seguire questa procedura per visualizzare e gestire le prenotazioni.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Gli utenti con accesso con privilegi elevati possono gestire tutte le sottoscrizioni e i gruppi di gestione di Azure

È possibile elevare l'accesso di un [utente per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

Dopo aver eseguito l'accesso con privilegi elevati:

1. Passare a **Prenotazione** tutti i servizi per visualizzare tutte le  >   prenotazioni presenti nel tenant.
1. Per apportare modifiche alla prenotazione, aggiungere se stessi come proprietario dell'ordine di prenotazione usando controllo di accesso (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Concedere agli utenti il controllo degli accessi in base al ruolo di Azure per le singole prenotazioni

Gli utenti che hanno accesso proprietario alle prenotazioni e agli amministratori di fatturazione possono delegare la gestione dell'accesso per un singolo ordine di prenotazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
1. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
1. In Dettagli prenotazione selezionare l'ordine di prenotazione.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario**. Se si vuole concedere un accesso limitato, selezionare un ruolo diverso.
1. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
1. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).