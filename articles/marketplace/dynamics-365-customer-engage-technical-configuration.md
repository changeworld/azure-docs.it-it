---
title: Configurare Dynamics 365 for Customer Engagement & configurazione tecnica dell'offerta PowerApps Microsoft AppSource (Azure Marketplace)
description: Configurare Dynamics 365 for Customer Engagement & configurazione tecnica dell'offerta PowerApps Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820394"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Configurare Dynamics 365 for Customer Engagement per la configurazione tecnica & Power Apps'offerta

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla.

## <a name="offer-information"></a>Informazioni sull'offerta

**Il modello di licenza di** base determina il modo in cui i clienti vengono assegnati all'applicazione nell'interfaccia di amministrazione di CRM. Selezionare **Risorsa** per le licenze basate su istanza o **Utente** se le licenze vengono assegnate una per ogni tenant.

La **casella di controllo Requires S2S outbound and CRM Secure Store Access** (Richiede l'accesso all'archiviazione sicura S2S e CRM Secure Store) abilita la configurazione dell'accesso in uscita da server a server (S2S) o archivio sicuro CRM. Questa funzionalità richiede una considerazione speciale da parte del team di Dynamics 365 durante la fase di certificazione. Microsoft contatterà l'utente per completare alcuni passaggi aggiuntivi per supportare questa funzionalità.

Lasciare **vuoto l'URL di configurazione** dell'applicazione. è per uso futuro.

## <a name="crm-package"></a>Pacchetto CRM

Per **URL del percorso del pacchetto**, immettere l'URL di un account Archiviazione BLOB di Azure che contiene il file ZIP del pacchetto CRM caricato. Includere una chiave di firma di accesso condiviso di sola lettura per consentire a Microsoft di prelevare il pacchetto per la verifica.

> [!IMPORTANT]
> Per evitare un blocco di pubblicazione, assicurarsi che la data di scadenza nell'URL dell'archiviazione BLOB non sia scaduta. È possibile modificare la data accedendo al criterio. Si consiglia di impostare l'**ora di scadenza** su almeno un mese nel futuro.

Selezionare la casella There is more than one CRM package in my package file (È presente più di un pacchetto **CRM nel file del pacchetto),** se applicabile. In tal caso, assicurarsi di includere tutti i pacchetti nel file ZIP.

Per informazioni dettagliate su come creare il pacchetto e aggiornarne la struttura, vedere il [Passaggio 3: Creare un pacchetto AppSource per l'app](/powerapps/developer/common-data-service/create-package-app-appsource)

## <a name="crm-package-availability"></a>Disponibilità del pacchetto CRM

Selezionare **+ Aggiungi area** per specificare le aree geografiche in cui il pacchetto CRM sarà disponibile per i clienti. La distribuzione nelle aree sovrane seguenti richiede un'autorizzazione speciale e la convalida durante il processo di certificazione: [Germania](../germany/index.yml), [Cloud del US Gov](../azure-government/documentation-government-welcome.md) e TIP.

Per impostazione predefinita, l'**URL di configurazione dell'applicazione** immesso in precedenza verrà usato per ogni area. Se si preferisce, è possibile immettere un URL di configurazione dell'applicazione distinto per una o più aree specifiche.

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di spostamento a sinistra, **Co-selling con Microsoft.** Per informazioni sulla configurazione del co-selling con Microsoft (facoltativo), vedere [Co-selling partner engagement (Engagement dei partner di co-selling).](marketplace-co-sell.md) Se non si sta configurando il co-selling o si è terminato, continuare con **i passaggi successivi riportati di** seguito.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il contenuto supplementare](dynamics-365-customer-engage-supplemental-content.md)
