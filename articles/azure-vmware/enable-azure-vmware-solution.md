---
title: Richiedi quota host e Abilita soluzione VMware di Azure
description: Informazioni su come richiedere la quota o la capacità host e abilitare il provider di risorse della soluzione VMware di Azure. È anche possibile richiedere più host in un cloud privato della soluzione VMware di Azure esistente.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653168"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Richiedi quota host e Abilita soluzione VMware di Azure

In questa procedura si apprenderà come richiedere la quota o la capacità host e abilitare il provider di risorse della [soluzione VMware di Azure](introduction.md) , che Abilita il servizio. Prima di poter abilitare la soluzione VMware di Azure, è necessario inviare un ticket di supporto per allocare gli host. Se si dispone di un cloud privato della soluzione VMware di Azure esistente e si desidera allocare più host, si seguirà lo stesso processo.

>[!IMPORTANT]
>L'assegnazione degli host può richiedere alcuni giorni, a seconda del numero richiesto.  Quindi, richiedere le informazioni necessarie per il provisioning, in modo che non sia necessario richiedere un aumento della quota con la stessa frequenza.


Il processo generale è semplice e include due passaggi:
- Richiedere una quota/capacità host aggiuntiva per [i clienti EA](#request-host-quota-for-ea-customers) o [i clienti CSP](#request-host-quota-for-csp-customers) 
- Abilitare il provider di risorse Microsoft. AVS

## <a name="eligibility-criteria"></a>Criteri di idoneità

Sarà necessario un account Azure in una sottoscrizione di Azure. La sottoscrizione di Azure deve seguire uno dei criteri seguenti:

- Una sottoscrizione in un [contratto Enterprise di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
- Una sottoscrizione gestita da Cloud Solution Provider (CSP) in un provider CSP esistente di Azure offre un contratto o un piano di Azure.

## <a name="request-host-quota-for-ea-customers"></a>Richiedi quota host per clienti con contratto Enterprise

1. Nella portale di Azure in Guida e **supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** Selezionare la sottoscrizione
   - **Servizio:** Tutti i servizi > soluzione VMware di Azure
   - **Risorsa:** Domanda generale 
   - **Riepilogo:** Capacità necessaria
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:

   - POC o produzione 
   - Nome area
   - Numero di host
   - Altri dettagli

   >[!NOTE]
   >La soluzione VMware di Azure consiglia un minimo di tre host per avviare il cloud privato e per gli host con ridondanza N + 1. 

1. Selezionare **Verifica + crea** per inviare la richiesta.


## <a name="request-host-quota-for-csp-customers"></a>Richiedi quota host per i clienti CSP 

I CSP devono usare il centro per i [partner Microsoft](https://partner.microsoft.com) per abilitare la soluzione VMware di Azure per i clienti. Questo articolo usa il [piano CSP di Azure](/partner-center/azure-plan-lp) come esempio per illustrare la procedura di acquisto per i partner.

Accedere al portale di Azure usando la procedura di **amministrazione per conto della** (Aobo) del centro per i partner.

>[!IMPORTANT] 
>Il servizio della soluzione VMware di Azure non fornisce un multi-tenant richiesto. I partner di hosting che lo richiedono non sono supportati. 

1. Configurare il piano di Azure CSP:

   1. In **centro** per i partner selezionare **CSP** per accedere all'area **clienti** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Area clienti del centro per i partner Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Selezionare il cliente, quindi selezionare **Aggiungi prodotti**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Selezionare **piano di Azure** e quindi selezionare **Aggiungi al carrello**. 
   
   1. Esaminare e completare la configurazione generale della sottoscrizione al piano Azure per il cliente. Per ulteriori informazioni, vedere la documentazione del centro per i [partner Microsoft](/partner-center/azure-plan-manage).

1. Dopo aver configurato il piano di Azure e avere a disposizione le autorizzazioni di controllo degli accessi in base al ruolo di [Azure](/partner-center/azure-plan-manage) necessarie per la sottoscrizione, verrà richiesta la quota per la sottoscrizione del piano Azure. 

   1. Accedere portale di Azure dal [centro per i partner Microsoft](https://partner.microsoft.com) usando la procedura **di amministrazione per conto di** (Aobo).
   
   1. Selezionare **CSP** per accedere all'area **Customers** .
   
   1. Espandere Dettagli cliente e selezionare **portale di gestione di Microsoft Azure**.
   
   1. In portale di Azure in **Guida e supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
      - **Tipo di problema:** Tecnici
      - **Sottoscrizione:** Selezionare la sottoscrizione
      - **Servizio:** Tutti i servizi > soluzione VMware di Azure
      - **Risorsa:** Domanda generale 
      - **Riepilogo:** Capacità necessaria
      - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
      - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)
   
   1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:
   
      - POC o produzione 
      - Nome area
      - Numero di host
      - Altri dettagli
      - È destinato a ospitare più clienti?
   
      >[!NOTE]
      >La soluzione VMware di Azure consiglia un minimo di tre host per avviare il cloud privato e per gli host con ridondanza N + 1. 
   
   1. Selezionare **Verifica + crea** per inviare la richiesta.

## <a name="register-the-microsoftavs-resource-provider"></a>Registrare il provider di risorse **Microsoft. AVS**

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Passaggi successivi

Una volta abilitata la risorsa e la rete corretta sul posto, è possibile [creare un cloud privato](tutorial-create-private-cloud.md).
