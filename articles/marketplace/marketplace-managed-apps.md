---
title: Guida alla pubblicazione dell'offerta di applicazioni gestite di Azure-Azure Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di un'applicazione gestita in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 09badebab86da8e4fd8d35307aa6648a26e91821
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232257"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guida alla pubblicazione per le applicazioni gestite di Azure

Un'offerta di *applicazione gestita* di Azure è un modo per pubblicare un'applicazione Azure in Azure Marketplace. Le applicazioni gestite sono offerte per transazioni distribuite e fatturate tramite Azure Marketplace. L'opzione di elenco visualizzata da un utente è *Get Now*.

Questo articolo illustra i requisiti per il tipo di offerta di applicazione gestita.

Usare il tipo di offerta di applicazione gestita nelle condizioni seguenti:

- Si sta distribuendo una soluzione basata sulla sottoscrizione per il cliente usando una macchina virtuale (VM) o un'intera soluzione basata su infrastruttura distribuita come servizio (IaaS).
- L'utente o il cliente richiede che la soluzione sia gestita da un partner.

>[!NOTE]
>Un partner può essere ad esempio un integratore di sistemi o un provider di servizi gestiti (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisiti dell'offerta di applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Una sottoscrizione di Azure | Le applicazioni gestite devono essere distribuite nella sottoscrizione di un cliente, ma possono essere gestite da terze parti. |
|Fatturazione e misurazione    |  Le risorse vengono fornite nella sottoscrizione di Azure di un cliente. Le risorse di Azure che usano il modello di pagamento con pagamento in base al consumo vengono sottoposte a transazione con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente. <br><br> Per le risorse di Azure con licenza Bring-Your-Own, Microsoft addebita tutti i costi di infrastruttura sostenuti per la sottoscrizione del cliente, ma è possibile effettuare direttamente le spese di licenza software con il cliente.        |
|Un pacchetto di applicazione gestita di Azure    |   Il modello di Azure Resource Manager configurato e creare la definizione dell'interfaccia utente che verrà usata per distribuire l'applicazione nella sottoscrizione del cliente.<br><br>Per ulteriori informazioni sulla creazione di un'applicazione gestita, vedere [Cenni preliminari sulle applicazioni gestite](../azure-resource-manager/managed-applications/publish-service-catalog-app.md).|

---

> [!NOTE]
> Le applicazioni gestite devono essere distribuibili tramite Azure Marketplace. Se la comunicazione dei clienti rappresenta un problema, contattare i clienti interessati dopo aver abilitato la condivisione dei lead.  

> [!Note]
> È ora disponibile un canale partner del provider di soluzioni cloud (CSP). Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, scoprire come [Aumentare il business sul cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Per eseguire la registrazione e iniziare a usare il Centro per i partner:

- [Accedere al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare l'offerta.
- Per altre informazioni, vedere [creare un'offerta di applicazione Azure](./create-new-azure-apps-offer.md) .
