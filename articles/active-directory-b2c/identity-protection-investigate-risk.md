---
title: Esaminare i rischi con Azure Active Directory B2C Identity Protection
description: Informazioni su come analizzare gli utenti a rischio e i rilevamenti in Azure AD B2C Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055703"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Esaminare i rischi con la protezione delle identità in Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection offre il rilevamento di rischi continuo per il tenant di Azure AD B2C. Consente alle organizzazioni di individuare, analizzare e correggere i rischi basati sulle identità. Identity Protection è costituito da report di rischio che possono essere usati per analizzare i rischi di identità in Azure AD B2C tenant. Questo articolo illustra come analizzare e ridurre i rischi.

## <a name="overview"></a>Panoramica

Azure AD B2C Identity Protection fornisce due report. Il report *utenti a rischio* è il punto in cui gli amministratori possono trovare gli utenti a rischio e i dettagli relativi ai rilevamenti. Il report *rilevamento dei rischi* fornisce informazioni su ogni rilevamento dei rischi, inclusi il tipo, altri rischi attivati allo stesso tempo, il percorso del tentativo di accesso e altro ancora.

Ogni report viene avviato con un elenco di tutti i rilevamenti relativi al periodo indicato nella parte superiore del report. I report possono essere filtrati usando i filtri disponibili nella parte superiore del report. Gli amministratori possono scegliere di scaricare i dati oppure usare [MS API Graph e Microsoft Graph PowerShell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) per esportare i dati in modo continuativo.

## <a name="service-limitations-and-considerations"></a>Limitazioni e considerazioni sui servizi

Quando si usa Identity Protection, tenere presente quanto segue:

- Identity Protection è attivato per impostazione predefinita.
- Identity Protection è disponibile sia per le identità locali che per quelle di social networking, ad esempio Google o Facebook. Per le identità di social networking, è necessario attivare l'accesso condizionale. Il rilevamento è limitato perché le credenziali dell'account di social networking sono gestite dal provider di identità esterno.
- In Azure AD B2C tenant è disponibile solo un subset di [Azure ad Identity Protection rilevamento dei rischi](../active-directory/identity-protection/overview-identity-protection.md) . I rilevamenti di rischio seguenti sono supportati da Azure AD B2C:  

|Tipo di rilevamento dei rischi  |Descrizione  |
|---------|---------|
| Trasferimento atipico     | Accedere da una posizione atipica basata sugli accessi recenti dell'utente.        |
|Indirizzo IP anonimo     | Accesso da un indirizzo IP anonimo (ad esempio: Tor Browser, VPN Anonymizer).        |
|Indirizzo IP collegato a malware     | Accesso da un indirizzo IP collegato al malware.         |
|Proprietà di accesso insolite     | Accesso con proprietà non rilevate di recente per l'utente specificato.        |
|L'amministratore ha confermato che l'utente è compromesso    | Un amministratore ha indicato che un utente è stato compromesso.             |
|Password spraying     | Eseguire l'accesso tramite un attacco spray per la password.      |
|Intelligence per le minacce di Azure AD     | Le origini di intelligence Microsoft per le minacce interne ed esterne hanno identificato uno schema di attacco noto.        |

## <a name="pricing-tier"></a>Piano tariffario

Azure AD B2C Premium P2 è necessario per alcune funzionalità di protezione delle identità. Se necessario, [modificare il piano tariffario di Azure AD B2C in Premium P2](./billing.md). La tabella seguente riepiloga le funzionalità di Identity Protection e il piano tariffario necessario.  

|Funzionalità   |P1   |P2|
|----------|:-----------:|:------------:|
|Report utenti a rischio     |&#x2713; |&#x2713; |
|Dettagli report utenti a rischio  | |&#x2713; |
|Correzione del report per gli utenti rischiosi    | &#x2713; |&#x2713; |
|Report rilevamento rischi   |&#x2713;|&#x2713;|
|Dettagli del rapporto sui rilevamenti dei rischi  ||&#x2713;|
|Download del report |  &#x2713;| &#x2713;|
|Accesso MS API Graph |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Esaminare gli utenti a rischio

Con le informazioni contenute nel report utenti a rischio gli amministratori possono trovare:

- Quali utenti sono a rischio, per quali utenti sono stati risolti i rischi o sono stati ignorati i rischi
- Dettagli sui rilevamenti
- Cronologia di tutti gli accessi a rischio
- Cronologia rischio
 
Gli amministratori possono quindi scegliere di intervenire su questi eventi. E possono scegliere di:

- Reimpostare la password dell'utente
- Confermare la compromissione dell'utente
- Ignorare il rischio utente
- Impedire all'utente di accedere
- Eseguire ulteriori analisi usando Azure ATP

### <a name="navigating-the-risky-users-report"></a>Esplorazione del report utenti a rischio

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.

1. In **Sicurezza** selezionare **Utenti a rischio (anteprima)** .

   ![Utenti a rischio](media/identity-protection-investigate-risk/risky-users.png)

    La selezione di singole voci espande una finestra dei dettagli sotto i rilevamenti. La visualizzazione dei dettagli consente agli amministratori di analizzare ed eseguire azioni su ogni rilevamento.

    ![Azioni utenti a rischio](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Report rilevamento rischi

Il report rilevamento rischi contiene dati filtrabili per un massimo di 90 giorni (tre mesi).

Con le informazioni contenute nel report rilevamenti di rischi gli amministratori possono trovare:

- Informazioni su ogni rilevamento di rischi, incluso il tipo.
- Altri rischi attivati nello stesso momento.
- Percorso del tentativo di accesso.

Gli amministratori possono quindi scegliere di tornare al report utenti a rischio o accessi a rischio per eseguire azioni in base alle informazioni raccolte.

### <a name="navigating-the-risk-detections-report"></a>Esplorazione del rapporto sui rilevamenti dei rischi

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Sicurezza** selezionare **Rilevamenti dei rischi (anteprima)** .

   ![Rilevamenti dei rischi](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere l'accesso condizionale a un flusso utente](conditional-access-user-flow.md).