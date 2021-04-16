---
title: 'Gestione delle applicazioni: procedure consigliate e raccomandazioni | Microsoft Docs'
description: Informazioni sulle procedure consigliate e le raccomandazioni per la gestione delle applicazioni in Azure Active Directory. Informazioni sull'uso del provisioning automatico e della pubblicazione di app locali con Application Proxy.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9b7f312781fd4f14c5e403ad72e5978f4d01487
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379324"
---
# <a name="application-management-best-practices"></a>Procedure consigliate per la gestione delle applicazioni

Questo articolo contiene raccomandazioni e procedure consigliate per la gestione delle applicazioni in Azure Active Directory (Azure AD), l'uso del provisioning automatico e la pubblicazione di app locali con Application Proxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Raccomandazioni per l'app cloud e l'accesso Single Sign-On
| Recommendation | Commenti |
| --- | --- |
| Controllare la raccolta Azure AD di applicazioni per le app  | Azure AD include una raccolta che contiene migliaia di applicazioni pre-integrate abilitate con Enterprise Single Sign-On (SSO). Per indicazioni sulla configurazione specifica dell'app, vedere [l'elenco di esercitazioni sulle app SaaS.](../saas-apps/tutorial-list.md)  | 
| Usare l'accesso SSO federato basato su SAML  | Quando un'applicazione la supporta, usare l'accesso SSO federato basato su SAML con Azure AD invece di SSO basato su password e ADFS.  | 
| Usare SHA-256 per la firma del certificato  | Azure AD usa l'algoritmo SHA-256 per impostazione predefinita per firmare la risposta SAML. Usare SHA-256 a meno che l'applicazione non richieda SHA-1 (vedere [Opzioni](certificate-signing-options.md) di firma del certificato e [Problema di accesso all'applicazione).](application-sign-in-problem-application-error.md)  | 
| Richiedere l'assegnazione utente  | Per impostazione predefinita, gli utenti possono accedere alle applicazioni aziendali senza essere assegnati. Tuttavia, se l'applicazione espone i ruoli o se si vuole che l'applicazione venga visualizzata nel App personali di un utente, richiedere l'assegnazione dell'utente.  | 
| Distribuire App personali agli utenti | [App personali](end-user-experiences.md) è un portale basato sul Web che offre agli utenti un singolo punto di ingresso per le applicazioni basate `https://myapps.microsoft.com` sul cloud assegnate. Con l'aggiunta di funzionalità aggiuntive come la gestione dei gruppi e la reimpostazione della password self-service, gli utenti possono trovarle in App personali. Vedere [Pianificare App personali distribuzione](my-apps-deployment-plan.md).
| Usare l'assegnazione di gruppo  | Se incluso nella sottoscrizione, assegnare gruppi a un'applicazione in modo da poter delegare la gestione degli accessi in corso al proprietario del gruppo.  | 
| Stabilire un processo per la gestione dei certificati | La durata massima di un certificato di firma è di tre anni. Per evitare o ridurre al minimo l'interruzione a causa della scadenza di un certificato, usare ruoli e liste di distribuzione di posta elettronica per garantire che le notifiche di modifica correlate ai certificati siano monitorate attentamente. |

## <a name="provisioning-recommendations"></a>Raccomandazioni sul provisioning
| Recommendation | Commenti |
| --- | --- |
| Usare le esercitazioni per configurare il provisioning con le app cloud | Per istruzioni dettagliate sulla configurazione del provisioning per l'app della raccolta da aggiungere, vedere l'elenco delle esercitazioni sulle [app SaaS.](../saas-apps/tutorial-list.md) |
| Usare i log di provisioning (anteprima) per monitorare lo stato | I [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) forniscono informazioni dettagliate su tutte le azioni eseguite dal servizio di provisioning, incluso lo stato per i singoli utenti. |
| Assegnare un gruppo di distribuzione al messaggio di posta elettronica di notifica del provisioning | Per aumentare la visibilità degli avvisi critici inviati dal servizio di provisioning, assegnare un gruppo di distribuzione all'impostazione Messaggi di posta elettronica di notifica. |


## <a name="application-proxy-recommendations"></a>Application Proxy raccomandazioni
| Recommendation | Commenti |
| --- | --- |
| Usare Application Proxy per l'accesso remoto alle risorse interne | Application Proxy è consigliabile concedere agli utenti remoti l'accesso alle risorse interne, sostituendo la necessità di una VPN o un proxy inverso. Non è destinato ad accedere alle risorse dall'interno della rete aziendale perché potrebbe aggiungere latenza.
| Usare domini personalizzati | Configurare domini personalizzati per le applicazioni (vedere Configurare domini [personalizzati)](application-proxy-configure-custom-domain.md)in modo che gli URL per gli utenti e tra le applicazioni funzionino dall'interno o dall'esterno della rete. Sarà anche possibile controllare la personalizzazione e personalizzare gli URL.  Quando si usano nomi di dominio personalizzati, pianificare l'acquisizione di un certificato pubblico da un'autorità di certificazione non Microsoft attendibile. applicazione Azure Proxy supporta i certificati standard, ([con caratteri](application-proxy-wildcard.md)jolly ) o basati su SAN. Vedere [Application Proxy pianificazione.](application-proxy-deployment-plan.md) |
| Sincronizzare gli utenti prima di distribuire Application Proxy | Prima di distribuire il proxy di applicazione, sincronizzare le identità utente da una directory locale o crearle direttamente Azure AD. sincronizzazione delle identità consente Azure AD pre-autenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate del proxy applicazione. Fornisce anche le informazioni necessarie sull'identificatore utente per eseguire l'accesso Single Sign-On (SSO). Vedere Application Proxy [pianificazione.](application-proxy-deployment-plan.md) |
| Seguire i suggerimenti per la disponibilità elevata e il bilanciamento del carico | Per informazioni sul flusso del traffico tra utenti, connettori Application Proxy e server app back-end e per ottenere suggerimenti per ottimizzare le prestazioni e il bilanciamento del carico, vedere Disponibilità elevata e bilanciamento del carico delle applicazioni e dei connettori [Application Proxy.](application-proxy-high-availability-load-balancing.md) |
| Usare più connettori | Usare due o più connettori Application Proxy per una maggiore resilienza, disponibilità e scalabilità (vedere Application Proxy [connettori](application-proxy-connectors.md)). Creare gruppi di connettori e assicurarsi che ogni gruppo di connettori abbia almeno due connettori (tre connettori sono ottimali). |
| Individuare i server connettore vicini ai server applicazioni e assicurarsi che siano nello stesso dominio | Per ottimizzare le prestazioni, individuare fisicamente il server connettore vicino ai server applicazioni (vedere [Considerazioni sulla topologia di rete).](application-proxy-network-topology.md) Inoltre, il server connettore e i server applicazioni Web devono appartenere allo stesso dominio di Active Directory o devono estendersi a domini trusting. Questa configurazione è necessaria per l'accesso SSO con autenticazione integrata di Windows (IWA) e la delega vincolata Kerberos (KCD). Se i server si verificano in domini diversi, è necessario usare la delega basata sulle risorse per l'accesso SSO .Vedere Delega vincolata Kerberos per l'accesso [Single Sign-On con Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Abilitare gli aggiornamenti automatici per i connettori | Abilitare gli aggiornamenti automatici per i connettori per le funzionalità e le correzioni di bug più recenti. Microsoft offre il supporto diretto per la versione più recente del connettore e una versione precedente. Vedere la [Application Proxy della versione.](application-proxy-release-version-history.md) |
| Ignorare il proxy locale | Per semplificare la manutenzione, configurare il connettore per ignorare il proxy locale in modo che si connetta direttamente ai servizi di Azure. Vedere Application Proxy [connettori e server proxy.](application-proxy-configure-connectors-with-proxy-servers.md) |
| Usare Azure AD Application Proxy web Application Proxy | Usare Azure AD Application Proxy per la maggior parte degli scenari locali. La Application Proxy Web è preferibile solo negli scenari che richiedono un server proxy per AD FS e in cui non è possibile usare domini personalizzati in Azure Active Directory. Vedere la [Application Proxy migrazione.](application-proxy-migration.md) |