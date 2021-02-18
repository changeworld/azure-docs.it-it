---
title: Modelli di hub di notifica di Azure
description: Informazioni sull'uso dei modelli per hub di notifica di Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635643"
---
# <a name="notification-hubs-templates"></a>Modelli di hub di notifica

I modelli consentono alle applicazioni client di specificare il formato esatto delle notifiche da ricevere. Mediante i modelli, le app possono ottenere diversi vantaggi, fra cui i seguenti:

- Un back-end indipendente dalla piattaforma
- Notifiche personalizzate
- Indipendenza dalla versione del client
- Localizzazione semplice

Questa sezione fornisce due esempi dettagliati su come usare i modelli per inviare notifiche indipendenti dalla piattaforma destinate a tutti i dispositivi su più piattaforme e per personalizzare le notifiche di broadcast per ogni dispositivo.

## <a name="using-templates-cross-platform"></a>Utilizzo di modelli multipiattaforma

Il metodo standard per inviare notifiche push consiste nell'inviare, per ciascuna notifica, uno specifico payload dei servizi di notifica della piattaforma (WNS, servizio APN). Per inviare un avviso al servizio APN, ad esempio, il payload è un oggetto JSON nel formato seguente:

```json
{"aps": {"alert" : "Hello!" }}
```

Per inviare un messaggio di avviso popup simile in un'applicazione di Windows Store, il payload XML è il seguente:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

È possibile creare payload simili per MPNS (Windows Phone) e piattaforme FCM (Android).

Tale requisito impone al back-end dell'app la produzione di payload diversi per ciascuna piattaforma e di fatto rende il back-end responsabile di parte del livello di presentazione dell'app. Alcuni dei problemi riguardano la localizzazione e i layout grafici (soprattutto per applicazioni di Windows Store che comprendono notifiche per vari tipi di riquadri).

La funzionalità del modello degli Hub di notifica consente a un'app client di creare registrazioni speciali, denominate registrazioni con modello, comprendenti un modello, oltre al set di tag. La funzionalità del modello di hub di notifica consente a un'app client di associare i dispositivi ai modelli se si lavora con le installazioni (scelta consigliata) o le registrazioni. Considerati gli esempi di payload precedenti, l'unica informazione indipendente dalla piattaforma è il messaggio di avviso effettivo (**Hello!**). Un modello è un set di istruzioni per l'hub di notifica su come formattare un messaggio indipendente dalla piattaforma per la registrazione di quell'app client specifica. Nell'esempio precedente il messaggio indipendente dalla piattaforma consiste in una singola proprietà: `message = Hello!`.

Nella figura riportata di seguito viene illustrato il processo:

![Diagramma che illustra il processo di utilizzo dei modelli multipiattaforma](./media/notification-hubs-templates/notification-hubs-hello.png)

Il modello per la registrazione dell'app client iOS è il seguente:

```json
{"aps": {"alert": "$(message)"}}
```

Il modello corrispondente per l'applicazione client di Windows Store è:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Si noti che il messaggio effettivo viene sostituito con l'espressione `$(message)` . Questa espressione indica all'hub di notifica, ogni volta che invia un messaggio a questa particolare registrazione, per compilare un messaggio che lo segue e inserisce il valore comune.

Se si utilizza il modello di installazione, la chiave "templates" di installazione include un JSON di più modelli. Se si utilizza il modello di registrazione, l'applicazione client può creare più registrazioni per poter utilizzare più modelli; ad esempio, un modello per i messaggi di avviso e un modello per gli aggiornamenti dei riquadri. Inoltre, le applicazioni client possono unire registrazioni native (registrazioni senza modello) e registrazioni con modello.

L'hub di notifica invia una notifica per ogni modello senza considerare se appartiene alla stessa app client. Questo comportamento può essere utilizzato per convertire notifiche indipendenti dalla piattaforma in più notifiche. Ad esempio, lo stesso messaggio indipendente dalla piattaforma all'hub di notifica può essere tradotto senza interruzioni in un avviso di tipo avviso popup e un aggiornamento del riquadro, senza richiedere al back-end di conoscerlo. Alcune piattaforme (ad esempio, iOS) potrebbero comprimere più notifiche nello stesso dispositivo se vengono inviate entro un breve periodo di tempo.

## <a name="using-templates-for-personalization"></a>Utilizzo di modelli per la personalizzazione

Un altro vantaggio derivante dall'utilizzo di modelli è la possibilità di utilizzare gli Hub di notifica per eseguire la personalizzazione delle notifiche per registrazione. Si consideri, ad esempio, un'app Weather che visualizza un riquadro con le condizioni meteorologiche in una posizione specifica. Un utente può scegliere tra gradi Celsius o Fahrenheit e una previsione singola o di cinque giorni. Mediante i modelli, ciascuna installazione dell'app client può registrare il formato richiesto (1 giorno in gradi Celsius 1 giorno in gradi Fahrenheit, 5 giorni in gradi Celsius, 5 giorni in gradi Fahrenheit), e far sì che il back-end invii un unico messaggio contenente tutte le informazioni necessarie per compilare i modelli (ad esempio una previsione di cinque giorni con gradi Celsius e Fahrenheit).

Il modello per la previsione di un giorno in gradi Celsius è il seguente:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Il messaggio inviato all'hub di notifica contiene tutte le proprietà seguenti:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Utilizzando questo modello, il back-end invia solo un unico messaggio senza dover memorizzare opzioni di personalizzazione specifiche per gli utenti dell'app. Questo scenario viene illustrato nell'immagine seguente:

![Diagramma che illustra il modo in cui il back-end invia un singolo messaggio a ogni piattaforma.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Come registrare i modelli

Per eseguire la registrazione con i modelli usando il modello di installazione (scelta consigliata) o il modello di registrazione, vedere [gestione delle registrazioni](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguaggio di espressione dei modelli

I modelli sono limitati ai formati di documento XML o JSON. Inoltre, è possibile inserire le espressioni solo in punti specifici; ad esempio, gli attributi o i valori dei nodi per XML, i valori delle proprietà stringa per JSON.

Nella tabella seguente viene descritto il linguaggio consentito nei modelli:

| Expression       | Descrizione |
| ---------------- | --- |
| $(prop)          | Riferimento a una proprietà di evento con il nome specificato. I nomi delle proprietà non distinguono tra maiuscole e minuscole. Questa espressione viene risolta nel valore di testo della proprietà o in una stringa vuota se la proprietà non è presente. |
|$(prop, n)       | Come in precedenza, ma il testo viene esplicitamente troncato dopo n caratteri, ad esempio $(title, 20) tronca il contenuto della proprietà del riquadro dopo 20 caratteri. |
| .(prop, n)      | Come in precedenza, ma vengono aggiunti tre punti alla fine del testo troncato. La dimensione totale della stringa ritagliata e il suffisso non superano i n caratteri. (title, 20) con una proprietà di input "Questa è la riga del titolo" restituisce **il titolo...** |
| %(prop)          | È simile a $(name), a eccezione del fatto che l'output è codificato in formato URI. |
| #(prop)          | Utilizzata nei modelli JSON (ad esempio, per modelli iOS e Android).<br><br>Questa funzione funziona esattamente come ' $ (prop)' specificato in precedenza, tranne quando viene usato nei modelli JSON (ad esempio, i modelli Apple). In questo caso, se questa funzione non è racchiusa tra "{','}" (ad esempio,' myJsonProperty ':' # (Name)') e restituisce un numero in formato JavaScript, ad esempio regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, il JSON di output è un numero.<br><br>Ad esempio,' badge:' # (Name)' diventa ' badge ': 40 (e non '40 '). |
| ' text ' o ' text ' | Valore letterale. I valori letterali contengono testo arbitrario racchiuso tra virgolette singole o doppie. |
| expr1 + expr2    | Operatore di concatenazione che unisce due espressioni in una singola stringa. |

Il formato delle espressioni può essere uno dei precedenti.

Quando si usa la concatenazione, l'intera espressione deve essere racchiusa tra `{}`. Ad esempio: `{$(prop) + ‘ - ’ + $(prop2)}`.

Ad esempio, il modello XML seguente non è valido:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Come spiegato in precedenza, quando si usa la concatenazione, le espressioni devono essere racchiuse tra parentesi graffe. Ad esempio:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
