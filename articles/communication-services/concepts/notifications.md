---
title: Notifiche in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Inviare notifiche agli utenti di app basate su Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d2b77708609f61eeb4ce33148f020027d646836b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813599"
---
# <a name="communication-services-notifications"></a>Notifiche di Servizi di comunicazione

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Le librerie client Chat e Chiamate di Servizi di comunicazione creano un canale di messaggistica in tempo reale che consente di eseguire il push dei messaggi di segnalazione nei client connessi in modo efficiente e affidabile. In questo modo è possibile integrare funzionalità avanzate di comunicazione in tempo reale nelle applicazioni senza la necessità di implementare una logica di polling HTTP complessa. Nelle applicazioni per dispositivi mobili però questo canale di segnalazione rimane connesso solo quando l'applicazione è attiva in primo piano. Per consentire agli utenti di ricevere chiamate in arrivo o messaggi di chat mentre l'applicazione è in background, occorre usare le notifiche push.

Le notifiche push permettono di inviare informazioni dall'applicazione ai dispositivi mobili degli utenti. È possibile usarle per visualizzare una finestra di dialogo, riprodurre un suono o visualizzare l'interfaccia utente delle chiamate in arrivo. Servizi di comunicazione di Azure supporta l'integrazione con [Griglia di eventi di Azure](../../event-grid/overview.md) e [Hub di notifica di Azure](../../notification-hubs/notification-hubs-push-notification-overview.md) che consentono di aggiungere notifiche push alle app.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Attivare le notifiche push tramite Griglia di eventi di Azure

L'integrazione tra Servizi di comunicazione di Azure e [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) consente di inviare notifiche degli eventi in tempo reale in modo affidabile, scalabile e sicuro. È possibile sfruttare questa integrazione per creare un servizio di notifica che recapiti le notifiche push sui dispositivi mobili degli utenti creando una sottoscrizione di Griglia di eventi che attiva una [funzione di Azure](../../azure-functions/functions-overview.md) o un webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagramma che mostra l'integrazione di Servizi di comunicazione con Griglia di eventi.":::

Per altre informazioni, vedere [Gestione degli eventi in Servizi di comunicazione di Azure](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Recapitare notifiche push con Hub di notifica di Azure

È possibile connettere un hub di notifica di Azure alla risorsa di Servizi di comunicazione per inviare automaticamente notifiche push al dispositivo mobile di un utente quando riceve una chiamata in arrivo. Usare queste notifiche push per riattivare l'applicazione dal background e visualizzare l'interfaccia utente che consente all'utente di accettare o rifiutare la chiamata. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagramma che mostra l'integrazione di Servizi di comunicazione con Hub di notifica di Azure.":::

Servizi di comunicazione usa Hub di notifica di Azure come servizio pass-through per comunicare con i vari servizi di notifica push specifici della piattaforma usando l'API di [invio diretto](/rest/api/notificationhubs/direct-send). Questo consente di riutilizzare le risorse e le configurazioni esistenti di Hub di notifica di Azure per recapitare alle applicazioni notifiche di chiamata affidabili e a bassa latenza.

> [!NOTE]
> Attualmente sono supportate solo le notifiche push per le chiamate.

### <a name="notification-hub-provisioning"></a>Provisioning di un hub di notifica 

Per recapitare le notifiche push ai dispositivi client tramite Hub di notifica, [creare un hub di notifica](../../notification-hubs/create-notification-hub-portal.md) nella stessa sottoscrizione della risorsa di Servizi di comunicazione. È necessario configurare l'hub di notifica di Azure per il sistema PNS (Platform Notification System) che si vuole usare. Per informazioni su come ottenere notifiche push nell'app client da Hub di notifica, vedere [Introduzione a Hub di notifica](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) e selezionare la piattaforma client di destinazione dall'elenco a discesa nella parte superiore della pagina.

> [!NOTE]
> Attualmente sono supportate le piattaforme APN e FCM.  
La piattaforma Apple Push Notification Service deve essere configurata con la modalità di autenticazione tramite token. Attualmente la modalità di autenticazione del certificato non è supportata. 

Una volta configurato l'hub di notifica, è possibile associarlo alla risorsa di Servizi di comunicazione fornendo la stringa di connessione dell'hub tramite il client di Azure Resource Manager o il portale di Azure. La stringa di connessione deve contenere le autorizzazioni `Send`. È consigliabile creare un altro criterio di accesso con le sole autorizzazioni `Send` specificamente per l'hub. Per altre informazioni, vedere [Sicurezza di Hub di notifica](../../notification-hubs/notification-hubs-push-notification-security.md).

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Uso del client di Azure Resource Manager per collegare l'hub di notifica

Per accedere ad Azure Resource Manager, eseguire il comando seguente ed effettuare l'accesso con le proprie credenziali.

```console
armclient login
```

 Eseguire quindi il comando seguente per effettuare il provisioning dell'hub di notifica:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Uso del portale di Azure per collegare l'hub di notifica

Nel portale passare alla risorsa di Servizi di comunicazione di Azure. All'interno della risorsa di Servizi di comunicazione selezionare Notifiche push nel menu a sinistra della pagina di Servizi di comunicazione e connettere l'hub di notifica di cui è stato effettuato il provisioning in precedenza. È necessario specificare qui la stringa di connessione e l'ID risorsa:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Screenshot che mostra le impostazioni di Notifiche push nel portale di Azure.":::

> [!NOTE]
> Se la stringa di connessione dell'hub di notifica di Azure viene aggiornata, è necessario aggiornare anche la risorsa di Servizi di comunicazione.  
Qualsiasi modifica apportata al modo in cui l'hub è collegato si rifletterà nel piano dati, ossia quando viene inviata una notifica, entro un intervallo massimo di ``10`` minuti. Ciò vale anche quando l'hub viene collegato per la prima volta **se** erano già state inviate notifiche.

### <a name="device-registration"></a>Registrazione del dispositivo 

Per informazioni su come registrare l'handle del dispositivo con Servizi di comunicazione, vedere la [guida di avvio rapido sulle chiamate vocali](../quickstarts/voice-video-calling/getting-started-with-calling.md).

### <a name="troubleshooting-guide-for-push-notifications"></a>Guida alla risoluzione dei problemi relativi alle notifiche push

Se le notifiche push non vengono visualizzate nel dispositivo, le cause possibili sono tre:

- Hub di notifica di Azure non ha accettato la notifica da Servizi di comunicazione di Azure
- Il sistema PNS (Platform Notification System), ad esempio il servizio APN e FCM, non ha accettato la notifica da Hub di notifica di Azure
- Il sistema Platform Notification System non ha recapitato la notifica al dispositivo.

La prima causa di mancato recapito di una notifica (Hub di notifica di Azure non ha accettato la notifica da Servizi di comunicazione di Azure) è illustrata di seguito. Per le altre due cause, vedere [Diagnosticare i problemi relativi alle notifiche non recapitate in Hub di notifica di Azure](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Un modo per verificare se la risorsa Servizi di comunicazione invia notifiche a Hub di notifica di Azure consiste nell'esaminare la metrica `incoming messages` dalle [metriche dell'hub di notifica di Azure](../../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs) collegate.

Di seguito sono descritti alcuni errori di configurazione che potrebbero essere la causa per cui l'hub di notifica di Azure non accetta le notifiche dalla risorsa Servizi di comunicazione.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>L'hub di notifica di Azure non è collegato alla risorsa Servizi di comunicazione

È possibile che l'hub di notifica di Azure non sia stato collegato alla risorsa Servizi di comunicazione. Per informazioni su come collegarli, vedere la sezione [Provisioning di un hub di notifica](#notification-hub-provisioning).

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>L'hub di notifica di Azure collegato non è configurato

È necessario configurare l'hub di notifica collegato con le credenziali del sistema Platform Notification System per la piattaforma (ad esempio iOS o Android) che si vuole usare. Per altre informazioni su come eseguire questa operazione, vedere [Configurare notifiche push in un hub di notifica](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>L'hub di notifica di Azure collegato non esiste

L'hub di notifica di Azure collegato alla risorsa Servizi di comunicazione non esiste più. Verificare che l'hub di notifica collegato esista ancora.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>La piattaforma Apple Push Notification Service dell'hub di notifica di Azure è configurata con la modalità di autenticazione del certificato

Non è possibile usare la piattaforma Apple Push Notification Service con la modalità di autenticazione del certificato in quanto non è attualmente supportata. Occorre configurare la piattaforma Apple Push Notification Service con la modalità di autenticazione token come specificato in [Configurare notifiche push in un hub di notifica](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>La stringa di connessione collegata non ha l'autorizzazione `Send`

La stringa di connessione usata per collegare l'hub di notifica alla risorsa Servizi di comunicazione deve avere l'autorizzazione `Send`. Per altre informazioni su come creare una nuova stringa di connessione o visualizzare la stringa di connessione corrente dall'hub di notifica di Azure, vedere [Sicurezza di Hub di notifica](../../notification-hubs/notification-hubs-push-notification-security.md).

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>La stringa di connessione collegata o l'ID risorsa dell'hub di notifica di Azure non sono validi

Assicurarsi di configurare la risorsa Servizi di comunicazione con la stringa di connessione e l'ID risorsa dell'hub di notifica di Azure corretti

#### <a name="the-linked-connection-string-is-regenerated"></a>La stringa di connessione collegata è stata rigenerata

Se la stringa di connessione dell'hub di notifica di Azure collegato è stata rigenerata, è necessario aggiornare la stringa di connessione con quella nuova nella risorsa Servizi di comunicazione [collegando di nuovo l'hub di notifica](#notification-hub-provisioning).

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](../../event-grid/overview.md)
* Per altre informazioni sui concetti di Hub di notifica di Azure, vedere [Documentazione di Hub di notifica di Azure](../../notification-hubs/index.yml)