---
title: Visualizzare l'utilizzo della prenotazione di Azure
description: Informazioni su come ottenere l'utilizzo della prenotazione e i dettagli.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568947"
---
# <a name="view-reservation-utilization-after-purchase"></a>Visualizzare l'utilizzo della prenotazione dopo l'acquisto

È possibile visualizzare la percentuale di utilizzo della prenotazione e le risorse che hanno usato la prenotazione nel portale di Azure e nell'app gestione Power BI costi.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Visualizzare l'utilizzo nell'ambiente di portale di Azure controllo degli accessi in base al ruolo di Azure

Per visualizzare l'utilizzo della prenotazione, è necessario avere accesso al controllo degli accessi in base al ruolo di Azure alla prenotazione oppure avere accesso con privilegi elevati per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. L'elenco mostra tutte le prenotazioni per le quali si ha il ruolo di Proprietario o Lettore. Ogni prenotazione mostra l'ultima percentuale di utilizzo nota.
1. Selezionare la percentuale di utilizzo per visualizzare la cronologia di utilizzo e i dettagli. Il video seguente mostra un esempio.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Visualizzare l'utilizzo come amministratore della fatturazione

Un amministratore Contratto Enterprise (EA) o un amministratore della fatturazione Contratto del cliente Microsoft (MCA) può visualizzare l'utilizzo da Gestione dei costi **e fatturazione.**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Gestione dei costi e prenotazioni** di  >  **fatturazione.**
1. Selezionare la percentuale di utilizzo per visualizzare la cronologia di utilizzo e i dettagli.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Ottenere i dettagli delle prenotazioni e l'utilizzo con le API, PowerShell e l'interfaccia della riga di comando

È possibile ottenere l'utilizzo [della prenotazione usando](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) l'API di utilizzo dell'istanza riservata.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Visualizzare le prenotazioni e l'utilizzo in Power BI

Sono disponibili due opzioni per Power BI utenti:

- Gestione costi di Azure connettore per Power BI Desktop: la data di acquisto della prenotazione e i dati di utilizzo sono disponibili nel connettore [Gestione costi di Azure per Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Creare i report desiderati usando il connettore .
- Gestione costi di Azure Power BI app: usare [l'app Gestione costi di Azure Power BI per](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) i report creati in precedenza che è possibile personalizzare ulteriormente.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md).
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md).
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](/partner-center/azure-reservations).
