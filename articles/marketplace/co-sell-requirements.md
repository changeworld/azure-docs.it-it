---
title: Requisiti di co-selling | Azure Marketplace
description: Per informazioni sui requisiti, è necessario che un'offerta del Marketplace commerciale Microsoft soddisfi per ottenere lo stato di incentivate di co-selling pronto o di co-selling.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/17/2021
ms.openlocfilehash: e67219e0b029ce401ffc05c009a5cc4a96680a9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593391"
---
# <a name="co-sell-requirements"></a>Requisiti di co-selling

Questo articolo fornisce i requisiti per i vari livelli di stato di co-selling. Per l'elenco più recente dei tipi di offerta che supportano la co-selling, vedere  [configurare la co-selling per un'offerta di Marketplace commerciale](co-sell-configure.md). Per una panoramica di co-selling, vedere la pagina relativa [alla co-selling con i team di vendita e i partner Microsoft](co-sell-overview.md).

Questa tabella Mostra tutti i possibili stati di co-selling:

| Stato | Commento |
| ------------ | ------------- |
| Non co-selling pronto | I [requisiti minimi per lo stato pronto per il co-selling](#requirements-for-co-sell-ready-status) non sono stati soddisfatti. |
| Co-selling pronto | Tutti i [requisiti per lo stato di co-selling pronto](#requirements-for-co-sell-ready-status) sono stati soddisfatti. |
| Co-selling IP di Azure incentivate | Sono stati soddisfatti i requisiti di co-selling pronti, oltre a [questi requisiti aggiuntivi](#requirements-for-azure-ip-co-sell-incentivized-status). |
| Business Applications co-selling incentivate | Questo stato si applica alle offerte di Dynamics 365 e Power Apps nel [programma Microsoft Business Applications ISV Connect](business-applications-isv-program.md) e indica che sono stati soddisfatti tutti i [requisiti per questo stato](#requirements-for-business-applications-co-sell-incentivized-status) . |
|||

## <a name="requirements-for-co-sell-ready-status"></a>Requisiti per lo stato pronto per il co-selling

Per un'offerta per ottenere lo stato pronto per il co-selling, è necessario soddisfare i requisiti seguenti:

**Tutti i partner**:

- Avere un ID MPN e un [account Marketplace commerciale attivo nel centro per i partner](./partner-center-portal/create-account.md).
- Assicurarsi di disporre di un [profilo di business](/partner-center/create-a-marketing-profile) completo nel centro per i partner. In qualità di partner Microsoft qualificato, il tuo profilo aziendale ti aiuta a presentare la tua attività ai clienti che cercano soluzioni e competenze esclusive per soddisfare le esigenze aziendali, ottenendo [riferimenti](/partner-center/referrals).
- Completare la **co-selling con** la scheda Microsoft e pubblicare l'offerta nel Marketplace commerciale.
- Fornire un contatto per le vendite per ogni co-selling geography idoneo e la fattura di materiali richiesta.

**Partner dei servizi**:

- Per le offerte del tipo di _soluzione del servizio_ , è necessario disporre di una competenza Gold attiva in qualsiasi area di competenza.

**Business Applications ISV**:

- Dynamics 365 & le soluzioni PowerApps (ad eccezione di Dynamics 365 business Central) richiedono la registrazione di ISV Connect.

### <a name="complete-the-co-sell-with-microsoft-tab"></a>Completare la co-selling con la scheda Microsoft

Quando si pubblica o si aggiorna l'offerta, fornire tutte le informazioni necessarie nella scheda **co-selling con Microsoft** , come descritto in [configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md). Sono inclusi i documenti seguenti:

- Soluzione/offerta con un cercapersone
- Piattaforma per soluzioni/offerte

Sono disponibili modelli per semplificare la creazione di questi documenti. Per ulteriori informazioni sulle informazioni obbligatorie e facoltative per il co-selling con la scheda Microsoft, vedere [configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md).

### <a name="publish-your-offer-live"></a>Pubblica l'offerta in tempo reale

Per qualificarsi per lo stato pronto per il co-selling, l'offerta o la soluzione deve essere pubblicata in tempo reale in almeno uno dei negozi online del Marketplace commerciale: Azure Marketplace o Microsoft AppSource. Per informazioni sulla pubblicazione di offerte nel Marketplace commerciale, vedere la [Guida alla pubblicazione in base al tipo di offerta](publisher-guide-by-offer-type.md). Se non è ancora stata pubblicata un'offerta nel Marketplace commerciale, assicurarsi di disporre di un [account Marketplace commerciale](./partner-center-portal/create-account.md).

## <a name="requirements-for-azure-ip-co-sell-incentivized-status"></a>Requisiti per lo stato di incentivate di co-selling IP di Azure

Lo stato di incentivate di co-sell IP di Azure si applica ai tipi di offerte seguenti:

- Applicazione di Azure
- Contenitore di Azure
- Macchina virtuale di Azure
- Modulo IoT Edge
- Software come un servizio (SaaS, Software as a Service)

Dopo aver ottenuto lo stato di co-selling pronto, esistono tre requisiti aggiuntivi per ottenere lo stato di incentivate di co-selling IP di Azure:

Requisito 1: ottenere i risultati seguenti:

- A _livello di organizzazione_, generare almeno $100.000 USD di soglia dei ricavi consumati da Azure nel periodo di 12 mesi finale. Questa operazione può essere ottenuta tramite una combinazione di soluzioni di Azure. Se l'offerta è transazionale nel Marketplace commerciale, è possibile soddisfare questo requisito rispettando una soglia fatturata di $100.000 USD.

Requisito 2: passare la convalida tecnica Microsoft per una soluzione basata su Azure:
- La convalida tecnica deve confermare che più del 50% dell'infrastruttura dell'offerta Usa codice IP ripetibile in Azure. Si noti che le macchine virtuali di Azure transazionali e le soluzioni applicazione Azure nel Marketplace commerciale soddisferanno questo requisito per impostazione predefinita.

Requisito 3: fornire un diagramma dell'architettura di riferimento:
- Caricare un diagramma dell'architettura di riferimento con i documenti di co-selling nel centro per i partner per la revisione. Per istruzioni sulla creazione di questo diagramma, vedere [diagramma dell'architettura di riferimento](reference-architecture-diagram.md). Per informazioni sul caricamento del diagramma, vedere Configurare la [co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md).

## <a name="requirements-for-business-applications-co-sell-incentivized-status"></a>Requisiti per Business Applications stato incentivate di co-selling

Questo stato si applica alle soluzioni basate su IP compilate in Dynamics 365 o Power Apps (ad eccezione di Dynamics 365 business Central) registrate nel programma ISV Connect. Tuttavia, le offerte devono anche completare i requisiti per lo stato di co-selling pronto (descritto in precedenza) per consentire ai venditori Microsoft di essere in grado di co-vendere l'offerta.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md)
