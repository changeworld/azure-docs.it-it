---
title: Configurare le notifiche tramite posta elettronica per gli avvisi del Centro sicurezza di Azure
description: Informazioni su come ottimizzare i tipi di messaggi di posta elettronica inviati dal Centro di sicurezza di Azure per gli avvisi di sicurezza.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 96a389a581a9ecaddfc418824b3ebe9c780e6bd1
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011588"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza 

Gli avvisi di sicurezza devono essere inviati alle persone giuste dell'organizzazione. Per impostazione predefinita, il Centro sicurezza invia i messaggi ai proprietari delle sottoscrizioni ogni volta che per queste viene generato un avviso con gravità alta. Questa pagina illustra come personalizzare queste notifiche.

Per definire le proprie preferenze per le notifiche tramite posta elettronica, la pagina di impostazioni **Notifiche tramite posta elettronica** del Centro sicurezza di Azure consente di scegliere:

- ***Chi* deve ricevere una notifica**: i messaggi di posta elettronica possono essere inviati a utenti selezionati o a chiunque abbia un ruolo di Azure specificato per una sottoscrizione. 
- ***Cosa* deve essere notificato**: modificare i livelli di gravità per cui il Centro sicurezza dovrà inviare notifiche.

Per evitare un sovraccarico di avvisi, il Centro sicurezza limita il volume dei messaggi di posta elettronica in uscita. Per ogni sottoscrizione, il Centro sicurezza invia:

- Un massimo di un messaggio di posta elettronica per **6 ore** (4 messaggi al giorno) per gli avvisi con **gravità alta**
- Un massimo di un messaggio di posta elettronica per **12 ore** (2 messaggi al giorno) per gli avvisi con **gravità media**
- Un massimo di un messaggio di posta elettronica per **24 ore** per gli avvisi con **gravità bassa**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Configurazione dei dettagli del contatto che riceverà messaggi di posta elettronica sugli avvisi di sicurezza." :::
 
## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|Livello gratuito|
|Autorizzazioni e ruoli obbligatori:|**Amministrazione della protezione**<br>**Proprietario della sottoscrizione** |
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Personalizzare le notifiche di posta elettronica degli avvisi di sicurezza tramite il portale<a name="email"></a>
È possibile inviare notifiche tramite posta elettronica a singoli utenti o a tutti quelli con ruoli specifici di Azure.

1. Nell'area **Prezzi e impostazioni** del Centro sicurezza selezionare la sottoscrizione appropriata e quindi selezionare **Notifiche tramite posta elettronica**.

1. Definire i destinatari per le notifiche con una o entrambe le opzioni seguenti:

    - Selezionare uno o più ruoli disponibili nell'elenco a discesa.
    - Immettere gli indirizzi di posta elettronica specifici separati da virgole. Non sono previsti limiti per il numero di indirizzi di posta elettronica che è possibile immettere.

1. Per applicare le informazioni di contatto per la sicurezza nella sottoscrizione, selezionare **Salva**.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Personalizzare le notifiche di posta elettronica degli avvisi tramite l'API
È anche possibile gestire le notifiche tramite posta elettronica tramite l'API REST fornita. Per i dettagli completi, vedere la [documentazione dell'API SecurityContacts](/rest/api/securitycenter/securitycontacts).

Questo è un corpo della richiesta di esempio per la richiesta PUT quando si crea una configurazione di contatto di sicurezza:

URI: https://management.azure.com/subscriptions/ <SubscriptionId> /providers/Microsoft.Security/securityContacts/default? API-Version = 2020-01-01-Preview

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Vedere anche
Per altre informazioni sugli avvisi di sicurezza, vedere le pagine seguenti:

- [Guida di riferimento sugli avvisi di sicurezza](alerts-reference.md): informazioni sugli avvisi di sicurezza che possono essere visualizzati nel modulo Protezione dalle minacce del Centro sicurezza di Azure
- [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Automazione del flusso di lavoro](workflow-automation.md): informazioni su come automatizzare le risposte agli avvisi con la logica delle notifiche personalizzate