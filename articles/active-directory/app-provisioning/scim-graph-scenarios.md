---
title: Usare SCIM, Microsoft Graph e Azure AD per effettuare il provisioning degli utenti e arricchire le app con i dati
description: L'uso di SCIM e Microsoft Graph insieme per effettuare il provisioning degli utenti e arricchire l'applicazione con i dati di cui ha bisogno.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530523"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>L'uso di SCIM e Microsoft Graph insieme per effettuare il provisioning degli utenti e arricchire l'applicazione con i dati di cui ha bisogno

**Destinatari:** Questo articolo è destinato agli sviluppatori che compilano applicazioni da integrare con Azure Active Directory (Azure AD). Se si sta cercando di usare applicazioni già integrate con Azure AD, ad esempio Zoom, ServiceNow e DropBox, è possibile ignorare questo articolo ed esaminare le esercitazioni specifiche dell'applicazione o esaminare il funzionamento del servizio [di provisioning.](./how-provisioning-works.md) [](../saas-apps/tutorial-list.md)

**Scenari comuni**

Azure AD fornisce un servizio out-of-the-box per il provisioning e una piattaforma estendibile su cui compilare le applicazioni. L'albero delle decisioni illustra come uno sviluppatore userebbe [SCIM](https://aka.ms/scimoverview) [e](/graph/overview) Microsoft Graph per automatizzare il provisioning. 

> [!div class="checklist"]
> * Creare automaticamente utenti nell'applicazione
> * Rimuovere automaticamente gli utenti dall'applicazione quando non dovrebbero più avere accesso
> * Integrare l'applicazione con più provider di identità per il provisioning
> * Arricchire l'applicazione con dati servizi Microsoft, ad esempio Teams, Outlook e Office.
> * Creare, aggiornare ed eliminare automaticamente utenti e gruppi in Azure AD Active Directory

![Albero delle decisioni scim graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: Creare automaticamente gli utenti nell'app
Attualmente, gli amministratori IT effettuano il provisioning degli utenti creando manualmente account utente o caricando periodicamente file CSV nell'applicazione. Il processo richiede molto tempo per i clienti e rallenta l'adozione dell'applicazione. Per creare un utente, sono necessarie solo informazioni utente di base, ad esempio nome, indirizzo di posta elettronica e userPrincipalName. 

**Raccomandazione**: 
* Se i clienti usano vari IDP e non si vuole mantenere un motore di sincronizzazione da integrare con ognuno, supportare un endpoint [/Users conforme](https://aka.ms/scimreferencecode) a SCIM. I clienti potranno usare facilmente questo endpoint per integrarsi con il servizio di provisioning Azure AD e creare automaticamente gli account utente quando necessitano dell'accesso. È possibile compilare l'endpoint una sola volta e sarà compatibile con tutti gli IDP. Vedere la richiesta di esempio seguente per informazioni sulla creazione di un utente tramite SCIM.
* Se sono necessari dati utente trovati nell'oggetto utente in Azure AD e altri dati da Microsoft, prendere in considerazione la creazione di un endpoint SCIM per il provisioning degli utenti e la chiamata al Microsoft Graph per ottenere il resto dei dati. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: Rimuovere automaticamente gli utenti dall'app
I clienti che usano l'applicazione sono incentrati sulla sicurezza e hanno requisiti di governance per rimuovere gli account quando i dipendenti non ne hanno più bisogno. Come è possibile automatizzare il deprovisioning dall'applicazione?

**Raccomandazione:** Supportare un endpoint /Users conforme a SCIM. Il Azure AD di provisioning invierà richieste di disabilitazione ed eliminazione quando l'utente non dovrebbe più avere accesso. È consigliabile supportare sia la disabilitazione che l'eliminazione degli utenti. Per informazioni sull'aspetto di una richiesta di disabilitazione ed eliminazione, vedere gli esempi seguenti. 

Disabilitare l'utente
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Eliminare un utente
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Automatizzare la gestione delle appartenenze ai gruppi nell'app
L'applicazione si basa su gruppi per l'accesso a varie risorse e i clienti vogliono riutilizzare i gruppi presenti in Azure AD. Come è possibile importare i gruppi Azure AD e mantenerli aggiornati quando cambiano le appartenenze?  

**Raccomandazione:** Supportare un [endpoint](https://aka.ms/scimreferencecode)/Groups conforme a SCIM. Il Azure AD di provisioning si occuperà della creazione di gruppi e della gestione degli aggiornamenti delle appartenenze nell'applicazione. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Arricchire l'app con i dati servizi Microsoft, ad esempio Teams, Outlook e OneDrive
L'applicazione è incorporata in Microsoft Teams e si basa sui dati dei messaggi. Vengono inoltre archiviati i file per gli utenti in OneDrive. Come è possibile arricchire l'applicazione con i dati di questi servizi e di Microsoft?

**Raccomandazione:** Il [Microsoft Graph](/graph/) è il punto di ingresso per accedere ai dati Microsoft. Ogni carico di lavoro espone le API con i dati necessari. Microsoft Graph può essere usato insieme al [provisioning SCIM](./use-scim-to-provision-users-and-groups.md) per gli scenari precedenti. È possibile usare SCIM per effettuare il provisioning degli attributi utente di base nell'applicazione durante la chiamata a Graph per ottenere tutti gli altri dati necessari. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Tenere traccia delle modifiche in servizi Microsoft, ad esempio Teams, Outlook e Azure AD
È necessario essere in grado di tenere traccia delle modifiche apportate ai messaggi di Teams e Outlook e di reagire in tempo reale. Come è possibile eseguire il push di queste modifiche nell'applicazione?

**Raccomandazione:** Il Microsoft Graph fornisce [notifiche di modifica e](/graph/webhooks) rilevamento delle [modifiche](/graph/delta-query-overview) per varie risorse. Tenere presente le limitazioni seguenti delle notifiche di modifica:
- Se un ricevitore di eventi riconosce un evento, ma non riesce ad agire per qualsiasi motivo, l'evento potrebbe andare perso.
- Non è garantito che l'ordine di ricezione delle modifiche sia cronologico.
- Le notifiche di modifica non contengono sempre i dati delle risorse [Per](/graph/webhooks-with-resource-data) i motivi indicati in precedenza, gli sviluppatori usano spesso le notifiche di modifica insieme al rilevamento delle modifiche per gli scenari di sincronizzazione. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Effettuare il provisioning di utenti e gruppi in Azure AD
L'applicazione crea informazioni su un utente necessarie per i clienti Azure AD. Potrebbe trattarsi di un'applicazione hr che gestisce le assunzioni, di un'app per le comunicazioni che crea numeri di telefono per gli utenti o di un'altra app che genera dati utili per Azure AD. Ricerca per categorie il record utente in Azure AD con i dati? 

**Raccomandazione** Microsoft Graph espone gli endpoint /Users e /Groups che è possibile integrare oggi per effettuare il provisioning degli utenti Azure AD. Si noti che Azure Active Directory non supporta la scrittura di tali utenti in Active Directory. 

> [!NOTE]
> Microsoft dispone di un servizio di provisioning che estrae i dati dalle applicazioni HR, ad esempio Workday e SuccessFactors. Queste integrazioni vengono create e gestite da Microsoft. Per l'onboarding di una nuova applicazione HR nel servizio, è possibile richiederla in [UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Articoli correlati

- [Esaminare la documentazione relativa ai Microsoft Graph sincronizzazione](/graph/api/resources/synchronization-overview)
- [Integrazione di un'app SCIM personalizzata con Azure AD](use-scim-to-provision-users-and-groups.md)