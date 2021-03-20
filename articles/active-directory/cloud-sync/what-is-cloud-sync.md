---
title: Che cos'è Azure AD Connect sincronizzazione cloud. | Microsoft Docs
description: Descrive Azure AD Connect sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b880d70baf7c160f8235bd01a5000f83825c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614488"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Che cos'è Azure AD Connect sincronizzazione cloud?
Azure AD Connect sincronizzazione cloud è una nuova offerta di Microsoft progettata per soddisfare e raggiungere gli obiettivi di identità ibrida per la sincronizzazione di utenti, gruppi e contatti con Azure AD.  Questa operazione viene eseguita tramite l'agente di provisioning Azure AD cloud invece dell'applicazione Azure AD Connect.  Tuttavia, può essere utilizzato insieme a Azure AD Connect Sync e offre i seguenti vantaggi:
    
- Supporto per la sincronizzazione con un tenant di Azure AD da un ambiente di Active Directory con più foreste disconnesse: gli scenari comuni includono fusioni e acquisizioni, in cui le foreste di AD dell'azienda acquisita sono isolate da quelle della casa madre, e le aziende che solitamente hanno più foreste di AD.
- Installazione semplificata con agenti di provisioning leggeri: gli agenti fungono da bridge tra AD e Azure AD, con tutta la configurazione di sincronizzazione gestita nel cloud. 
- Per semplificare le distribuzioni a disponibilità elevata, in particolare per le organizzazioni che si basano sulla sincronizzazione degli hash delle password tra AD e Azure AD, è possibile usare più agenti di provisioning.
- Supporto per gruppi di grandi dimensioni con un massimo di 50.000 membri. È consigliabile utilizzare solo il filtro di ambito OU durante la sincronizzazione di gruppi di grandi dimensioni.


![Cos'è Azure AD Connect](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>In che modo Azure AD Connect sincronizzazione cloud è diversa dalla sincronizzazione Azure AD Connect?
Con Azure AD Connect sincronizzazione cloud, il provisioning da AD a Azure AD è orchestrato in Microsoft Online Services. Le organizzazioni devono solo distribuire, nel proprio ambiente locale e in quello ospitato da IaaS, un agente leggero che funge da bridge tra Azure AD e AD. La configurazione del provisioning viene archiviata in Azure AD e gestita come parte del servizio.

## <a name="azure-ad-connect-cloud-sync-video"></a>Video sulla sincronizzazione del Cloud Azure AD Connect
Il breve video seguente offre una panoramica eccellente di Azure AD Connect la sincronizzazione cloud:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Confronto tra Azure AD Connect e la sincronizzazione cloud

Nella tabella seguente viene illustrato un confronto tra Azure AD Connect e Azure AD Connect la sincronizzazione cloud:

| Funzionalità | Servizio di sincronizzazione di Azure Active Directory Connect| Sincronizzazione Cloud Azure Active Directory Connect |
|:--- |:---:|:---:|
|Connessione a singola foresta AD locale|● |● |
| Connessione a più foreste AD locali |● |● |
| Connessione a più foreste di AD locali disconnesse | |● |
| Modello con installazione di agente leggero | |● |
| Più agenti attivi per la disponibilità elevata | |● |
| Connessione a directory LDAP|●| | 
| Supporto per oggetti utente |● |● |
| Supporto per oggetti gruppo |● |● |
| Supporto per oggetti contatto |● |● |
| Supporto per oggetti dispositivo |● | |
| Personalizzazione di base per i flussi di attributi |● |● |
| Sincronizzazione degli attributi online di Exchange |● |● |
| Sincronizzazione degli attributi di estensioni 1-15 |● |● |
| Sincronizzazione di attributi AD definiti dall'utente (estensioni della directory) |● | |
| Supporto per la sincronizzazione degli hash delle password |●|●|
| Supporto per l'autenticazione pass-through |●||
| Supporto per la federazione |●|●|
| Accesso Single Sign-On facile (Seamless SSO)|● |●|
| Supporta l'installazione in un controller di dominio |● |● |
| Supporto per Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Applicazioni di filtri in base a domini/unità organizzative/gruppi |● |● |
| Applicazione di filtri in base a valori di attributo degli oggetti |● | |
| Possibilità di sincronizzare un set minimo di attributi (MinSync) |● |● |
| Possibilità di interrompere il flusso di attributi da AD ad Azure AD |● |● |
| Personalizzazione avanzata dei flussi di attributi |● | |
| Supporto per il writeback (password, dispositivi, gruppi) |● | |
| Supporto per Azure AD Domain Services|● | |
| [Writeback della distribuzione ibrida Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Supporto per un massimo di 150.000 oggetti per dominio AD |● |● |
| Supporto per gruppi di grandi dimensioni con un massimo di 50.000 membri |● |● |
| Riferimenti tra domini|● | |
| Provisioning su richiesta| |● |

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Installare la sincronizzazione cloud](how-to-install.md)
