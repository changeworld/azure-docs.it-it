---
title: Funzionalità del Framework dell'interfaccia utente di servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services conceptual document
description: Informazioni sulle funzionalità del Framework dell'interfaccia utente
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 51caf1e73cdeda5862e033a05878f35ddb6ff8dc
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539908"
---
# <a name="ui-framework-capabilities"></a>Funzionalità del Framework dell'interfaccia utente

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Il Framework dell'interfaccia utente di servizi di comunicazione Azure consente di creare esperienze di comunicazione usando un set di componenti riutilizzabili. Questi componenti sono disponibili in due versioni: i componenti di **base** sono i blocchi predefiniti più semplici dell'esperienza dell'interfaccia utente, mentre le combinazioni di questi componenti di base sono denominate componenti **compositi** .

## <a name="ui-framework-composite-components"></a>Componenti compositi di Framework interfaccia utente

| Composite               | Descrizione                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Raggruppamento che chiama composito | Esperienza di chiamata in uscita vocale e video leggera per i servizi di comunicazione di Azure che chiamano usando asset di progettazione dell'interfaccia utente Fluent. Supporta il gruppo che chiama usando l'ID del gruppo di servizi di comunicazione di Azure. Il composito consente di usare una chiamata uno-a-uno facendo riferimento a un'identità dei servizi di comunicazione di Azure o a un numero di telefono per PSTN usando un numero di telefono acquistato tramite Azure.                                    | React |  |  |
| Composizione chat gruppo    | Esperienza di chat leggera per i servizi di comunicazione di Azure con risorse di progettazione dell'interfaccia utente Fluent. Questa esperienza si concentra sulla distribuzione di un semplice client di chat in grado di connettersi ai thread dei servizi di comunicazione di Azure. Consente agli utenti di inviare messaggi e visualizzare i messaggi ricevuti con gli indicatori di digitazione e le conferme di lettura. Viene ridimensionato da 1:1 a scenari di chat di gruppo. Supporta un solo thread di chat.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Componenti di base del Framework dell'interfaccia utente

| Componente             | Descrizione                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Provider chiamante    | Componente di inizializzazione principale per la chiamata a. Componente necessario per inizializzare gli altri componenti. Gestisce la logica di base per inizializzare il client chiamante usando i token di accesso dei servizi di comunicazione di Azure. Supporta il join di gruppo. | React | N/D     | N/D |
| Controlli multimediali   | Consente agli utenti di gestire la chiamata corrente attivando o disattivando il video e terminando la chiamata.                                                                                                                                                              | React | N/D     | N/D |
| Raccolta multimediale   | Mostra tutti i partecipanti alle chiamate in una singola raccolta. Raccolta supporta sia i partecipanti abilitati per il video che quelli statici. Per i partecipanti abilitati per video, viene eseguito il rendering del video.                                                                                                                | React | N/D     | N/D |
| Impostazioni del microfono | Selezionare il microfono da usare per la chiamata a. Questo controllo può essere usato prima e durante una chiamata per selezionare il dispositivo microfonico.                                                                                                                                               | React | N/D     | N/D |
| Impostazioni videocamera     | Selezionare la fotocamera da usare per la chiamata del video. Questo controllo può essere utilizzato prima e durante una chiamata per selezionare il dispositivo video.                                                                                                                                             | React | N/D     | N/D |
| Impostazioni del dispositivo     | Combina le impostazioni del microfono e della fotocamera in un singolo componente                                                                                                 | React | N/D     | N/D |
| Provider chat       | Componente di inizializzazione principale per la chat. Componente necessario per inizializzare gli altri componenti. Gestisce la logica di base per inizializzare il client di chat con un token di accesso di servizi di comunicazione di Azure e il thread a cui verrà aggiunto.                                     | React | N/D     | N/D |
| Casella di trasmissione          | Componente di input che consente agli utenti di inviare messaggi al thread di chat. L'input supporta testo, collegamenti ipertestuali, emoji e altri caratteri Unicode, inclusi altri alfabeti.                                                                                                                         | React | N/D     | N/D |
| Thread di chat           | Componente thread che mostra all'utente i messaggi ricevuti e inviati con le informazioni sul mittente. Il thread supporta la digitazione di indicatori e di conferme di lettura. È possibile scorrere questi thread per esaminare la cronologia delle chat.
| Elenco dei partecipanti      | Mostra tutti i partecipanti della chiamata o del thread di chat come elenco.  | React | N/D     | N/D |

## <a name="ui-framework-capabilities"></a>Funzionalità del Framework dell'interfaccia utente

| Funzionalità                                                             | Raggruppamento che chiama composito | Composizione chat gruppo | Componenti di base |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Unisciti ai team riunione                                                  |                         |                      |           
| Partecipa all'evento live di Teams                                               |                         |                      | 
| Avvia chiamata VoIP all'utente Teams                                       |                         |                      | 
| Partecipa a una chat di riunione sui team                                           |                         |                      |            
| Aggiunta della chiamata ai servizi di comunicazione di Azure con ID gruppo                | ✔                      |                      | ✔
| Avviare una chiamata VoIP a uno o più utenti di servizi di comunicazione di Azure |                         |                      |           
| Unisciti a un thread di chat di servizi di comunicazione Azure                    |                         | ✔                   | ✔
| Chiamate mute/unmute                                                    | ✔                       |                      | ✔
| Video acceso/spento alla chiamata                                                | ✔                       |                      | ✔
| Condivisione schermo                                                      | ✔                       |                      | ✔
| Raccolta partecipanti                                                 | ✔                       |                      | ✔
| Gestione del microfono                                               | ✔                       |                      | ✔
| Gestione della fotocamera                                                   | ✔                       |                      | ✔
| Lobby di chiamata                                                          |                         |                      | ✔
| Invia messaggio di chat                                                   |                         | ✔                   |            
| Ricevi messaggio di chat                                                |                         | ✔                   | ✔
| Digitazione di indicatori                                                   |                         | ✔                   | ✔
| Conferma di lettura                                                        |                         | ✔                   | ✔
| Elenco dei partecipanti                                                    |                         |                      | ✔


## <a name="customization-support"></a>Supporto per la personalizzazione

| Tipo di componente            | Temi     | Layout                                                              | Modelli di dati |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Componente composito       |     N/D    | N/D                                                                 |     N/D     |
| Componente di base            |     N/D    | Il layout dei componenti può essere modificato usando lo stile esterno         |     N/D     |


## <a name="platform-support"></a>Piattaforme supportate

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| SDK DELL'INTERFACCIA UTENTE | Chrome \* , nuovo perimetro | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Si noti che la versione più recente di Chrome è supportata in aggiunta alle due versioni precedenti.

\*\*Si noti che le versioni di Safari 13.1 + sono supportate. Il video in uscita per Safari macOS non è ancora supportato, ma è supportato in iOS. La condivisione dello schermo in uscita è supportata solo in iOS per desktop.
