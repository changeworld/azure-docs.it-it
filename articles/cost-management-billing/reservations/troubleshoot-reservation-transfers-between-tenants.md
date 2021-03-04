---
title: Risolvere i trasferimenti di prenotazione di Azure tra tenant
description: Questo articolo aiuta i proprietari delle prenotazioni a trasferire un ordine di prenotazione da un tenant Azure Active Directory (directory) a un altro.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055836"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Risolvere i problemi relativi ai trasferimenti di prenotazione tra i tenant

Questo articolo aiuta i proprietari delle prenotazioni a trasferire un ordine di prenotazione da un tenant Azure Active Directory (directory) a un altro. Quando si modifica la directory di un ordine di prenotazione, viene rimosso qualsiasi accesso RBAC di Azure all'ordine di prenotazione e alle prenotazioni dipendenti. Sarà possibile accedere solo dopo la modifica. La modifica della directory non comporta la modifica della proprietà della fatturazione per l'ordine di prenotazione. La directory viene modificata per l'ordine di prenotazione padre e per le prenotazioni dipendenti.

Per il trasferimento tra i tenant non è necessario un cambio di prenotazione e l'annullamento.

Dopo aver trasferito una prenotazione a un altro tenant, potrebbe essere necessario aggiungere altri proprietari alla prenotazione. Per ulteriori informazioni, vedere [utenti che possono gestire una prenotazione per impostazione predefinita](view-reservations.md#who-can-manage-a-reservation-by-default).

Quando si trasferisce un ordine di prenotazione, vengono trasferite tutte le prenotazioni sotto l'ordine.

## <a name="transfer-a-reservation"></a>Trasferimento di una prenotazione

Usare la procedura seguente per trasferire un ordine di prenotazione e le prenotazioni dipendenti a un altro tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se non si è un amministratore della fatturazione, ma si è un proprietario della prenotazione, passare a **prenotazioni** , quindi andare al passaggio 6.
1. Passare a **Gestione dei costi e fatturazione**.
    - Se si è un amministratore EA, nel menu a sinistra selezionare **ambiti di fatturazione** e quindi nell'elenco degli ambiti di fatturazione selezionare uno.
    - Se si è proprietari del profilo di fatturazione del contratto clienti Microsoft, nel menu a sinistra selezionare **profili di fatturazione**. Nell'elenco dei profili di fatturazione selezionarne uno.
1. Nel menu a sinistra selezionare **transazioni di prenotazione**. Viene visualizzato l'elenco delle transazioni di prenotazione.
1. Un banner nella parte superiore della pagina legge *ora gli amministratori della fatturazione possono gestire le prenotazioni. Fare clic qui per gestire le prenotazioni.* Selezionare il banner.
1. Viene visualizzato l'elenco completo delle prenotazioni per la registrazione EA o il profilo di fatturazione.
1. Selezionare la prenotazione che si desidera trasferire.
1. In dettagli prenotazione selezionare l'ID dell'ordine di prenotazione.
1. Nell'ordine di prenotazione selezionare **Cambia directory**.
1. Nel riquadro Cambia directory selezionare la directory Azure AD a cui si vuole trasferire la prenotazione, quindi selezionare **conferma**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni, vedere [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md).