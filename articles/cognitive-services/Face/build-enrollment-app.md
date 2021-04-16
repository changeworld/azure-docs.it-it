---
title: Creare un'app React per aggiungere utenti a un servizio Viso
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare l'ambiente di sviluppo e distribuire un'app Viso per ottenere il consenso dai clienti.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505106"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Creare un'app React per aggiungere utenti a un servizio Viso

Questa guida illustra come iniziare a usare l'applicazione di registrazione viso di esempio. L'app illustra le procedure consigliate per ottenere un consenso significativo per aggiungere utenti a un servizio di riconoscimento volto e acquisire dati sul viso con accuratezza elevata. Un sistema integrato può usare un'app come questa per fornire il controllo di accesso senza tocco, la verifica dell'identità, il monitoraggio delle frequenze, la personalizzazione in modalità tutto schermo o la verifica dell'identità, in base ai dati del viso.

All'avvio, l'applicazione mostra agli utenti una schermata di consenso dettagliata. Se l'utente concede il consenso, l'app richiede un nome utente e una password e quindi acquisisce un'immagine del viso di alta qualità usando la fotocamera del dispositivo.

L'app di esempio viene scritta usando JavaScript e React Native framework. Attualmente può essere distribuito nei dispositivi Android. Altre opzioni di distribuzione verranno presto disponibili.

## <a name="prerequisites"></a>Prerequisiti 

* Una sottoscrizione di Azure: [crearne una gratuitamente.](https://azure.microsoft.com/free/cognitive-services/)  
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Viso nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.  
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso.  
  * Per lo sviluppo e il test locali, è possibile incollare la chiave API e l'endpoint nel file di configurazione. Per la distribuzione finale, archiviare la chiave API in una posizione sicura e mai nel codice.  

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di Servizi cognitivi. Non condividerle. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. È inoltre consigliabile rigenerare queste chiavi regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuato al servizio.

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

1. Clonare il repository Git per [l'app di esempio](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Per configurare l'ambiente di sviluppo, seguire la <a href="https://reactnative.dev/docs/environment-setup"  title=" React Native documentazione React Native documentazione "  target="_blank"> </a> . Selezionare **React Native guida introduttiva all'interfaccia** della riga di comando come sistema operativo di sviluppo e **selezionare Android** come sistema operativo di destinazione. Completare le sezioni **Installazione delle dipendenze e** **dell'ambiente di sviluppo Android.**
1. Aprire il env.jsfile nell'editor di testo preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/), e aggiungere l'endpoint e la chiave. È possibile ottenere l'endpoint e la chiave nel portale di Azure nella **scheda Panoramica** della risorsa. Questo passaggio è solo a scopo di test locale e non &mdash; archivia la chiave API Viso nel repository remoto.
1. Eseguire l'app usando l'emulatore di dispositivo virtuale Android Android Studio o il proprio dispositivo Android. Per testare l'app in un dispositivo fisico, seguire la documentazione <a href="https://reactnative.dev/docs/running-on-device"  title=" React Native "  target="_blank"> pertinente React Native documentazione </a> .  


## <a name="create-a-user-add-experience"></a>Creare un'esperienza di aggiunta utente  

Dopo aver configurato l'app di esempio, è possibile personalizzarla in base alle proprie esigenze.

Ad esempio, è possibile aggiungere informazioni specifiche della situazione nella pagina di consenso:

> [!div class="mx-imgBorder"]
> ![pagina di consenso dell'app](./media/enrollment-app/1-consent-1.jpg)

Il servizio offre controlli di qualità delle immagini che consentono di scegliere se l'immagine è di qualità sufficiente per aggiungere il cliente o tentare il riconoscimento del viso. Questa app illustra come accedere ai fotogrammi dalla fotocamera del dispositivo, selezionare i fotogrammi di alta qualità e aggiungere il viso rilevato nel servizio API Viso. 

Molti problemi di riconoscimento del viso sono causati da immagini di riferimento di bassa qualità. Alcuni fattori che possono ridurre le prestazioni del modello sono:
* Dimensioni del viso (visi distanti dalla fotocamera)
* Orientamento del viso (visi rivolti o inclinati dalla fotocamera)
* Condizioni di illuminazione scadenti (scarsa illuminazione o retroilluminazione) in cui l'immagine potrebbe essere esposta in modo non ingato o avere un rumore eccessivo
* Occlusione (visi parzialmente nascosti o ostruito) inclusi accessori come berre o occhiali con spessore
* Sfocatura (ad esempio tramite rapido movimento del viso quando è stata scattata la foto). 

> [!div class="mx-imgBorder"]
> ![Pagina di istruzioni per l'acquisizione di immagini dell'app](./media/enrollment-app/4-instruction.jpg)

Si noti che l'app offre anche funzionalità per l'eliminazione delle informazioni dell'utente e la possibilità di aggiungere di nuovo.

> [!div class="mx-imgBorder"]
> ![pagina di gestione del profilo](./media/enrollment-app/10-manage-2.jpg)

Per estendere le funzionalità dell'app per coprire [](enrollment-overview.md) l'esperienza completa, leggere la panoramica delle funzionalità aggiuntive da implementare e delle procedure consigliate.

## <a name="deploy-the-app"></a>Distribuire l'app

### <a name="android"></a>Android

Prima di tutto, assicurarsi che l'app sia pronta per la distribuzione di produzione: rimuovere eventuali chiavi o segreti dal codice dell'app e assicurarsi di aver seguito le procedure consigliate [per la sicurezza.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

Quando si è pronti per rilasciare l'app per la produzione, si genererà un file APK pronto per il rilascio, ovvero il formato di file del pacchetto per le app Android. Questo file APK deve essere firmato con una chiave privata. Con questa build di versione è possibile iniziare a distribuire l'app direttamente nei dispositivi. 

Per informazioni su come generare una chiave privata, firmare l'applicazione e generare un APK di rilascio, seguire la documentazione prepararsi per la <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> </a> versione.  

Dopo aver creato un APK firmato, vedere la documentazione Pubblicare l'app Per altre informazioni su come <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> </a> rilasciare l'app.

## <a name="next-steps"></a>Passaggi successivi  

In questa guida si è appreso come configurare l'ambiente di sviluppo e iniziare a usare l'app di esempio. Se non si ha di React Native, è [](https://reactnative.dev/docs/getting-started) possibile leggere la documentazione introduttiva per altre informazioni di base. Può anche essere utile acquisire familiarità con [l'API Viso.](Overview.md) Leggere le altre sezioni sull'aggiunta di utenti prima di iniziare lo sviluppo.