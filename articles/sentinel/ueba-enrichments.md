---
title: Informazioni di riferimento sugli arricchimenti di offrono dati di Sentinel di Azure | Microsoft Docs
description: Questo articolo Mostra gli arricchimenti delle entità generati dall'analisi del comportamento dell'entità di Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901727"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Informazioni di riferimento sugli arricchimenti di offrono dati di Sentinel di Azure

Queste tabelle elencano e descrivono le arricchimenti delle entità che possono essere usate per concentrarsi e affinare l'analisi degli eventi imprevisti della sicurezza.

Le prime due tabelle, **User Insights** e **Device Insights**, contengono informazioni sulle entità dalle origini Active Directory/Azure ad e Intelligence per le minacce di Microsoft.

<a name="baseline-explained"></a>Il resto delle tabelle, in **Activity Insights Tables**, contiene informazioni sulle entità basate sui profili comportamentali creati dall'analisi del comportamento dell'entità di Azure Sentinel. Le attività vengono analizzate rispetto a una baseline compilata dinamicamente ogni volta che viene usata. Ogni attività ha il periodo di lookback definito da cui viene derivata questa linea di base dinamica. Questo periodo viene specificato nella colonna [**baseline**](#activity-insights-tables) della tabella.

> [!NOTE] 
> Il campo **nome di arricchimento** in tutte e tre le tabelle Visualizza due righe di informazioni. Il primo, in **grassetto**, è il "nome descrittivo" dell'arricchimento. Il secondo *(in corsivo e parentesi)* è il nome del campo dell'arricchimento archiviato nella [**tabella di analisi del comportamento**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tabella User Insights

| Nome di arricchimento | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Nome visualizzato dell'account**<br>*(AccountDisplayName)* | Nome visualizzato dell'account dell'utente. | Amministratore, Hayden Cook |
| **Dominio account**<br>*(AccountDomain)* | Nome di dominio dell'account dell'utente. |  |
| **ID oggetto account**<br>*(AccountObjectID)* | ID dell'oggetto account dell'utente. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Raggio di esplosione**<br>*(BlastRadius)* | Il raggio Blast viene calcolato in base a diversi fattori: la posizione dell'utente nell'albero dell'organizzazione e i ruoli e le autorizzazioni Azure Active Directory dell'utente. | Basso, medio, elevato |
| **Account inattivo**<br>*(IsDormantAccount)* | L'account non è stato usato per gli ultimi 180 giorni. | True, False |
| **Amministratore locale**<br>*(IsLocalAdmin)* | L'account dispone di privilegi di amministratore locale. | True, False |
| **Nuovo account**<br>*(IsNewAccount)* | L'account è stato creato negli ultimi 30 giorni. | True, False |
| **SID locale**<br>*(OnPremisesSID)* | SID locale dell'utente correlato all'azione. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tabella di Device Insights

| Nome di arricchimento | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Browser**<br>*Browser* | Browser utilizzato nell'azione. | Edge, Chrome |
| **Famiglia di dispositivi**<br>*(DeviceFamily)* | Il gruppo di dispositivi usato nell'azione. | Windows |
| **Tipo di dispositivo**<br>*DeviceType* | Il tipo di dispositivo client utilizzato nell'azione | Desktop |
| **ISP**<br>*ISP* | Provider di servizi Internet usato nell'azione. |  |
| **Sistema operativo**<br>*OperatingSystem* | Sistema operativo usato nell'azione. | Windows 10 |
| **Descrizione indicatore di minaccia Intel**<br>*(ThreatIntelIndicatorDescription)* | Descrizione dell'indicatore di minaccia osservato risolto dall'indirizzo IP usato nell'azione. | L'host è membro di botnet: azorult |
| **Tipo di indicatore di minaccia Intel**<br>*(ThreatIntelIndicatorType)* | Tipo dell'indicatore di minaccia risolto dall'indirizzo IP usato nell'azione. | Botnet, C2, CryptoMining, darknet, DDoS, MaliciousUrl, malware, phishing, proxy, PUA, Watch List |
| **Agente utente**<br>*UserAgent* | Agente utente usato nell'azione. | Libreria client di Microsoft Azure Graph 1,0,<br>Spavalderia-codegen/1.4.0.0/CSharp,<br>EvoSTS |
| **Famiglia di agenti utente**<br>*(UserAgentFamily)* | Famiglia di agenti utente utilizzata nell'azione. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tabelle di Activity Insights

#### <a name="action-performed"></a>Azione eseguita

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Prima azione eseguita dall'utente**<br>*(FirstTimeUserPerformedAction)* | 180 | L'azione è stata eseguita per la prima volta dall'utente. | True, False |
| **Azione eseguita in modo insolito dall'utente**<br>*(ActionUncommonlyPerformedByUser)* | 10 | L'azione non viene in genere eseguita dall'utente. | True, False |
| **Azione eseguita in modo insolito tra i peer**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | L'azione non viene in genere eseguita tra i peer dell'utente. | True, False |
| **Prima azione eseguita nel tenant**<br>*(FirstTimeActionPerformedInTenant)* | 180 | L'azione è stata eseguita per la prima volta da chiunque nell'organizzazione. | True, False |
| **Azione eseguita in modo insolito nel tenant**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | L'azione non viene in genere eseguita nell'organizzazione. | True, False |
|

#### <a name="app-used"></a>App usata

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **App usata per la prima volta**<br>*(FirstTimeUserUsedApp)* | 180 | L'app è stata usata per la prima volta dall'utente. | True, False |
| **App utilizzata non comunemente dall'utente**<br>*(AppUncommonlyUsedByUser)* | 10 | L'app non viene comunemente usata dall'utente. | True, False |
| **App utilizzata in comune tra i peer**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | L'app non viene comunemente usata tra i peer dell'utente. | True, False |
| **App per la prima volta osservata nel tenant**<br>*(FirstTimeAppObservedInTenant)* | 180 | L'app è stata osservata per la prima volta nell'organizzazione. | True, False |
| **App usata in comune nel tenant**<br>*(AppUncommonlyUsedInTenant)* | 180 | L'app non viene comunemente usata nell'organizzazione. | True, False |
| 

#### <a name="browser-used"></a>Browser utilizzato

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Primo utente connesso tramite browser**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Il browser è stato rilevato per la prima volta dall'utente. | True, False |
| **Browser utilizzato non comunemente dall'utente**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Il browser non viene comunemente usato dall'utente. | True, False |
| **Browser usato in comune tra i peer**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Il browser non viene comunemente usato tra i peer dell'utente. | True, False |
| **Browser per la prima volta osservato nel tenant**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Il browser è stato rilevato per la prima volta nell'organizzazione. | True, False |
| **Browser usato in comune nel tenant**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Il browser non viene comunemente usato nell'organizzazione. | True, False |
| 

#### <a name="country-connected-from"></a>Paese connesso da

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Primo utente connesso dal paese**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | La posizione geografica, come risolta dall'indirizzo IP, è stata connessa da per la prima volta dall'utente. | True, False |
| **Paese non comune connesso dall'utente**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | La posizione geografica, come risolta dall'indirizzo IP, non è comunemente connessa dall'utente. | True, False |
| **Paese connesso in comune tra i peer**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | La posizione geografica, come risolta dall'indirizzo IP, non è comunemente connessa tra i peer dell'utente. | True, False |
| **Prima connessione dal paese osservato nel tenant**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Il paese è stato connesso da per la prima volta da chiunque nell'organizzazione. | True, False |
| **Paese connesso in comune da nel tenant**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | La posizione geografica, come risolta dall'indirizzo IP, non è in genere connessa da nell'organizzazione. | True, False |
| 

#### <a name="device-used-to-connect"></a>Dispositivo usato per la connessione

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Primo utente connesso dal dispositivo**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Il dispositivo di origine è stato connesso da per la prima volta dall'utente. | True, False |
| **Dispositivo utilizzato insolitamente dall'utente**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Il dispositivo non viene comunemente usato dall'utente. | True, False |
| **Dispositivo usato in comune tra i peer**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Il dispositivo non viene comunemente usato tra i peer dell'utente. | True, False |
| **Primo dispositivo rilevato nel tenant**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Il dispositivo è stato rilevato per la prima volta nell'organizzazione. | True, False |
| **Dispositivo usato in comune nel tenant**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Il dispositivo non viene comunemente usato nell'organizzazione. | True, False |
| 

#### <a name="other-device-related"></a>Altri elementi correlati al dispositivo

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Prima volta che l'utente si è connesso al dispositivo**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Il dispositivo di destinazione è stato connesso per la prima volta dall'utente. | True, False |
| **Famiglia di dispositivi usata in comune nel tenant**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | La famiglia di dispositivi non viene comunemente usata nell'organizzazione. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Provider di servizi Internet usato per la connessione

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Primo utente connesso tramite ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | Il provider di servizi Internet è stato rilevato per la prima volta dall'utente. | True, False |
| **ISP usato in comune dall'utente**<br>*(ISPUncommonlyUsedByUser)* | 10 | Il provider di servizi Internet non viene comunemente usato dall'utente. | True, False |
| **ISP usato in comune tra i peer**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Il provider di servizi Internet non viene comunemente usato tra i peer dell'utente. | True, False |
| **Connessione per la prima volta tramite ISP nel tenant**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Il provider di servizi Internet è stato rilevato per la prima volta nell'organizzazione. | True, False |
| **ISP usato in comune nel tenant**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Il provider di servizi Internet non viene comunemente usato nell'organizzazione. | True, False |
| 

#### <a name="resource-accessed"></a>Risorsa a cui si effettua l'accesso

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Prima risorsa a cui si accede per la prima volta**<br>*(FirstTimeUserAccessedResource)* | 180 | È stato eseguito l'accesso alla risorsa per la prima volta dall'utente. | True, False |
| **Risorsa a cui l'utente accede in comune**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | L'utente non accede comunemente alla risorsa. | True, False |
| **Risorsa a cui si accede in comune tra i peer**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | La risorsa non è comunemente accessibile tra i peer dell'utente. | True, False |
| **Prima risorsa a cui si accede nel tenant**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | È stato eseguito l'accesso alla risorsa per la prima volta da parte di tutti gli utenti dell'organizzazione. | True, False |
| **Risorsa a cui si accede in comune nel tenant**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | L'accesso alla risorsa non è in genere disponibile nell'organizzazione. | True, False |
| 

#### <a name="miscellaneous"></a>Varie

| Nome di arricchimento | [Baseline](#baseline-explained) (giorni) | Descrizione | Valore di esempio |
| --- | --- | --- | --- |
| **Ultima azione eseguita dall'utente**<br>*(LastTimeUserPerformedAction)* | 180 | Ultima volta in cui l'utente ha eseguito la stessa azione. | <Timestamp> |
| **Un'azione simile non è stata eseguita in precedenza**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | L'utente non ha eseguito alcuna azione nello stesso provider di risorse. | True, False |
| **Percorso IP di origine**<br>*(SourceIPLocation)* | *N/D* | Il paese è stato risolto dall'indirizzo IP di origine dell'azione. | [Surrey, Inghilterra] |
| **Volume elevato di operazioni non comuni**<br>*(UncommonHighVolumeOfOperations)* | 7 | Un utente ha eseguito una sequenza di operazioni simili nello stesso provider | True, False |
| **Numero insolito di errori di accesso condizionale Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Un numero insolito di utenti non è riuscito a eseguire l'autenticazione a causa dell'accesso condizionale | True, False |
| **Numero insolito di dispositivi aggiunti**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Un utente ha aggiunto un numero insolito di dispositivi. | True, False |
| **Numero insolito di dispositivi eliminati**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Un utente ha eliminato un numero insolito di dispositivi. | True, False |
| **Numero insolito di utenti aggiunti al gruppo**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Un utente ha aggiunto un numero insolito di utenti a un gruppo. | True, False |
|