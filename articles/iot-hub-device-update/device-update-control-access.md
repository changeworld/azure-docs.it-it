---
title: Informazioni sull'aggiornamento del dispositivo per l'autenticazione e l'autorizzazione dell'hub Internet | Microsoft Docs
description: Informazioni sul modo in cui l'aggiornamento dei dispositivi per l'hub Internet usa il controllo degli accessi in base al ruolo di Azure
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558398"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Controllo degli accessi in base al ruolo di Azure (RBAC) e aggiornamento del dispositivo

L'aggiornamento del dispositivo usa il controllo degli accessi in base al ruolo di Azure per fornire l'autenticazione e l'autorizzazione per

## <a name="configure-access-control-roles"></a>Configurare i ruoli di controllo di accesso

Per consentire ad altri utenti e applicazioni di accedere all'aggiornamento del dispositivo, è necessario concedere agli utenti o alle applicazioni l'accesso a questa risorsa. Ecco i ruoli supportati dall'aggiornamento del dispositivo

|   Nome ruolo   | Descrizione  |
| :--------- | :---- |
|  Amministratore degli aggiornamenti del dispositivo | Ha accesso a tutte le risorse di aggiornamento del dispositivo  |
|  Lettore di aggiornamento dispositivo| Consente di visualizzare tutti gli aggiornamenti e le distribuzioni |
|  Amministratore contenuto aggiornamento dispositivo | Consente di visualizzare, importare ed eliminare gli aggiornamenti  |
|  Lettore contenuto aggiornamento dispositivo | Consente di visualizzare gli aggiornamenti  |
|  Amministratore delle distribuzioni degli aggiornamenti del dispositivo | Consente di gestire la distribuzione degli aggiornamenti ai dispositivi|
|  Lettore di distribuzioni di aggiornamenti del dispositivo| Consente di visualizzare le distribuzioni di aggiornamenti ai dispositivi |

È possibile utilizzare una combinazione di ruoli per fornire il livello di accesso corretto. Uno sviluppatore può ad esempio importare e gestire gli aggiornamenti usando il ruolo di amministratore del contenuto di aggiornamento del dispositivo, ma necessita di un ruolo di lettore di distribuzioni di aggiornamenti del dispositivo per visualizzare lo stato di un aggiornamento. Viceversa, un operatore di soluzione con il ruolo lettore aggiornamento dispositivo può visualizzare tutti gli aggiornamenti, ma deve usare il ruolo di amministratore distribuzioni aggiornamento dispositivi per distribuire un aggiornamento specifico ai dispositivi.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Eseguire l'autenticazione a API REST per l'aggiornamento del dispositivo per la pubblicazione e la gestione

L'aggiornamento del dispositivo usa anche Azure AD per l'autenticazione per pubblicare e gestire il contenuto tramite le API del servizio. Per iniziare, è necessario creare e configurare un'applicazione client.

### <a name="create-client-azure-ad-app"></a>Creare App Azure AD client

Per integrare un'applicazione o un servizio con Azure AD, [registrare prima](../active-directory/develop/quickstart-register-app.md) un'applicazione con Azure ad. L'installazione dell'applicazione client varia a seconda del flusso di autorizzazione utilizzato.  La configurazione seguente è consigliata quando si usano le API REST per l'aggiornamento del dispositivo.

* Impostare l'autenticazione client:' URI di reindirizzamento per il client nativo o Web '.
* Impostare le autorizzazioni per l'API: l'aggiornamento del dispositivo per l'hub Internet viene esposto:
  * Autorizzazioni delegate:' user_impersonation '
  * **Facoltativo**, concedere il consenso dell'amministratore

[Passaggi successivi: creare risorse di aggiornamento del dispositivo e configurare i ruoli di controllo di accesso](./create-device-update-account.md)