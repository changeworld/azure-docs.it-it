---
title: Guida alla sicurezza di mini R per Azure Stack Edge | Microsoft Docs
description: Descrive le convenzioni di sicurezza, le linee guida, le considerazioni e spiega come installare e usare in modo sicuro il dispositivo Mini R Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382626"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Istruzioni per la protezione di mini R per Azure Stack Edge

![Icona di avviso 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ leggere icona ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **di avviso di sicurezza leggere informazioni sulla sicurezza e sull'integrità**

Leggere tutte le informazioni di sicurezza contenute in questo articolo prima di usare il dispositivo Mini R di Azure Stack Edge, una composizione di un pacco batteria, un alimentatore collegato AC/DC, un alimentatore modulo e un modulo server. Il mancato completamento delle istruzioni può causare incendi, scosse elettriche, lesioni o danni alle proprietà. Leggere tutte le informazioni di sicurezza riportate di seguito prima di usare Azure Stack Edge Mini R.

## <a name="safety-icon-conventions"></a>Convenzioni di sicurezza

Di seguito sono riportate le parole per i segnali di avviso di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Simbolo di rischio](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Pericolo:** Indica una situazione pericolosa che, se non evitata, provocherà morte o lesioni gravi. <br> **Avviso:** Indica una situazione pericolosa che, se non evitata, può comportare la morte o lesioni gravi. <br> **Attenzione:** Indica una situazione pericolosa che, se non evitata, può causare lesioni minime o moderate.|
|

Quando si configura e si esegue il dispositivo Mini R Azure Stack Edge, è necessario osservare le seguenti icone di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Leggere prima tutte le istruzioni](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Leggere prima tutte le istruzioni |
| ![Icona di notifica](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **NOTIFICA:** | Indica le informazioni considerate importanti, ma non correlate al rischio. |
| ![Simbolo di rischio](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Simbolo di rischio |
| ![Icona di scossa elettrica](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Rischio di scosse elettriche |
| ![Solo per uso interno](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Solo per uso interno |
| ![Icona di assenza di parti riparabili dall'utente](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nessun componente utilizzabile dall'utente. Non accedere a meno che non si sia stati adeguatamente formati. |
|

## <a name="handling-precautions-and-site-selection"></a>Gestione precauzioni e selezione sito

Il dispositivo Mini R Azure Stack Edge presenta le precauzioni di gestione seguenti e i criteri di selezione del sito:

![Icona di avviso 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ nessuna parte di servizio utilizzabile dall'utente ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Attenzione:**

* Esaminare il dispositivo *come è stato ricevuto* per individuare eventuali danni. Se lo chassis del dispositivo è danneggiato, [contattare supporto tecnico Microsoft](azure-stack-edge-placeholder.md) per ottenere una sostituzione. Non tentare di utilizzare il dispositivo.
* Se si ritiene che il dispositivo non funziona correttamente, [contattare supporto tecnico Microsoft](azure-stack-edge-placeholder.md) per ottenere una sostituzione. Non tentare di riparare il dispositivo.
* Il dispositivo non contiene parti riparabili dall'utente. All'interno sono presenti livelli pericolosi di tensione, corrente ed energia. Non aprire il dispositivo. Restituire il dispositivo a Microsoft per la manutenzione.

![Icona di avviso 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Si consiglia di utilizzare il sistema:

* Lontano dalle fonti di calore, tra cui la luce solare diretta e i radiatori.
* In posizioni non esposte a umidità o pioggia.
* Si trova in uno spazio che riduce al minimo la vibrazione e lo shock fisico.  Il sistema è progettato per urti e vibrazioni in base a MIL-STD-810G.
* Isolamento da campi elettromagnetici forti prodotti da dispositivi elettrici.
* Non consentire l'accesso al sistema di alcun oggetto liquido o di alcun oggetto esterno. Non inserire bevande o altri contenitori Liquid nel sistema o in prossimità del sistema.

![Icona di avviso 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ nessun componente utilizzabile dall'utente ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **, attenzione:**

* Questa apparecchiatura contiene una batteria al litio. Non tentare di eseguire il Service Pack. Le batterie in questo dispositivo non sono utilizzabili dall'utente. Rischio di esplosione se la batteria viene sostituita da un tipo non corretto.

![Icona di avviso 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Addebitare il pacco batteria solo quando fa parte del dispositivo Mini R Azure Stack Edge, non addebitare come dispositivo separato.

![Icona di avviso 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Il commutatore ON/OFF del pacco batteria è per l'esaurimento della batteria solo per il modulo server. Se la scheda di alimentazione è collegata al pacco batteria, Power viene passata al modulo server anche se il commutatore si trova nella posizione OFF.

![Icona di avviso 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Non masterizzare o cortocircuitare la batteria del pacchetto. Deve essere riciclata o eliminata correttamente.

![Icona di avviso 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Invece di usare l'alimentatore AC/DC fornito, questo sistema ha anche la possibilità di usare un campo fornito di tipo 2590 batteria. In questo caso, l'utente finale dovrà verificare che soddisfi tutti i requisiti di sicurezza, trasporto, ambiente e qualsiasi altra normativa nazionale/locale.
* Quando si utilizza il sistema con il tipo 2590 batteria, utilizzare la batteria entro le condizioni di utilizzo specificate dal produttore della batteria.

![Avviso icona 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Questo dispositivo ha due porte SFP +, che possono essere usate con i ricetrasmettitori ottici. Per evitare radiazioni laser pericolose, usare solo con i ricetrasmettitori di classe 1.

## <a name="electrical-precautions"></a>Precauzioni elettriche

Il dispositivo Mini R Azure Stack Edge presenta le precauzioni elettriche seguenti:

![Icona di avviso 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Avviso:**

Se usato con l'adattatore alimentatore:

* Fornire una connessione di terra elettrica sicura per i cavi di alimentazione. Il cavo corrente alternato (AC) dispone di un plug-in di base a tre fili (un plug con un pin di base). Questa spina può essere inserita solo in una presa CA con messa a terra. Non annullare lo scopo del pin di messa a terra.
* Dato che la spina del cavo di alimentazione è il principale dispositivo di disconnessione, accertarsi che si trovino prese vicine al dispositivo e che siano facilmente accessibili.
* Scollegare i cavi di alimentazione (estraendo il connettore, non il cavo) e scollegare tutti i cavi in presenza di una delle condizioni seguenti:

  * Il cavo di alimentazione o la spina presenta sfilacciature o danni di altro tipo.
  * Il dispositivo è esposto a pioggia, a umidità in eccesso o ad altri liquidi.
  * Il dispositivo è stato eliminato e la carcassa del dispositivo è stata danneggiata.
  * Si sospetta che il dispositivo richieda un intervento di manutenzione o riparazione.
* Scollegare definitivamente l'unità prima di spostare o se si ritiene che sia stata danneggiata in alcun modo.

* Fornire una fonte di alimentazione adatta con protezione da sovraccarichi elettrici per soddisfare le specifiche tecniche seguenti:

* Tensione: 100-240 volt AC
* Corrente: 1,7 ampere
* Frequenza: da 50 a 60 Hz

![Icona di avviso 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Avviso:**

* Non tentare di modificare o usare i cavi di alimentazione CA diversi da quelli forniti con l'apparecchiatura.

![Icona di avviso 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ interna solo ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **Avviso:**

* L'alimentatore con etichetta con questo simbolo viene valutato solo per uso interno.

## <a name="regulatory-information"></a>Informazioni sulle normative

Di seguito sono riportate informazioni normative per i dispositivi Mini R Azure Stack Edge, numero del modello normativo: TMA01.

Il dispositivo Mini R di Azure Stack Edge è progettato per l'uso con NRTL elencati (UL, CSA, ETL e così via) e IEC/EN 60950-1 o IEC/EN 62368-1 compliant (CE contrassegnato) apparecchiature it.

In paesi diversi dagli Stati Uniti e dal Canada, i cavi di rete (non forniti con l'apparecchiatura) non devono essere installati con questa apparecchiatura se la loro lunghezza è superiore a 3 metri.

L'attrezzatura è progettata per operare negli ambienti seguenti:

| Ambiente | Specifiche |
|:---  |:--- |
| Specifiche della temperatura del sistema | <ul><li>Temperatura di archiviazione: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Operazione continua: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; F)</li></ul> |
| Specifiche di umidità relativa (RH) | <ul><li>Archiviazione: umidità relativa dal 5% al 95%</li><li>Operativo: umidità relativa dal 10% al 90%</li></ul>|
| Specifiche di altitudine massima | <ul><li>Operativo: 15.000 metri (4.572 metri)</li><li>Non operativo: 40.000 metri (12.192 metri)</li></ul>|

> ![Icona di avviso-2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Avviso:** le &nbsp; modifiche o le modifiche apportate alle apparecchiature non espressamente approvate da Microsoft possono annullare l'autorità dell'utente per il funzionamento dell'apparecchiatura.

#### <a name="canada-and-usa"></a>CANADA e Stati Uniti:

> ![Icona di avviso-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Avviso:** &nbsp; Questa apparecchiatura è stata testata e risulta conforme ai limiti di una classe a un dispositivo digitale, in base alla parte 15 delle regole FCC. Questi limiti intendono fornire una protezione ragionevole da interferenze dannose quando l'attrezzatura si utilizzata in un ambiente commerciale. Questo dispositivo genera, usa e può irradiare energia a radiofrequenza e, se non installato e utilizzato in conformità con il manuale di istruzioni, potrebbe causare interferenze dannose alle comunicazioni radio. Il funzionamento di questa apparecchiatura in un'area residenziale può causare un'interferenza dannosa, nel qual caso l'utente dovrà correggere l'interferenza a proprie spese.

L'adattatore USB Netgear a6150 Wi-Fi fornito con questa apparecchiatura è concepito per essere operato vicino al corpo umano. viene testato per la conformità della velocità di assorbimento specifica (SAR) usata dal corpo. Il limite di SAR impostato dal FCC è 1,6 W/kg quando viene calcolata una media di oltre 1 g di tessuto. Quando si trasporta il prodotto o lo si utilizza quando si indossa il corpo, mantenere una distanza di 10 mm dal corpo per garantire la conformità ai requisiti di esposizione RF.

L'adattatore USB Netgear a6150 WiFi è conforme a ANSI/IEEE C 95.1-1999 ed è stato testato in base ai metodi e alle procedure di misurazione specificati in OET Bulletin 65 Supplemento C.

Velocità di assorbimento (SAR) specifica di Netgear a6150:1,18 W/kg di tempo medio su 1 g di tessuto

L'adattatore USB Netgear a6150 WiFi deve essere usato solo con antenne approvate. Questo dispositivo e le relative antenne non devono avere un percorso condiviso o funzionare insieme ad altre antenne o emittenti, tranne che in conformità alle procedure del prodotto FCC multitrasmettitore. Per i prodotti disponibili nel mercato degli Stati Uniti, è possibile operare solo con Channel 1 ~ 11. Non è possibile selezionare altri canali.

L'operazione nella banda 5150 – 5250 MHz è destinata solo all'uso interno per ridurre la possibilità di interferenze dannose per i sistemi satellite per dispositivi mobili co-canale.

![Avviso per informazioni normative-uso interno](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

Si consiglia agli utenti di allocare radar ad alta potenza come utenti primari (priorità utenti) delle bande 5250 – 5350 MHz e 5650-5850 MHz. questi radar potrebbero causare interferenze e/o danni ai dispositivi LE-LAN.

Questa apparecchiatura genera, USA e può irradiare energia Radio Frequency e, se non è installata e usata in conformità con le istruzioni, può causare interferenze pericolose per le comunicazioni radio. Tuttavia, non vi è alcuna garanzia che l'interferenza non venga eseguita in una particolare installazione.

Se questa apparecchiatura genera interferenze pericolose per la ricezione di radio o televisori, che può essere determinata dalla disattivazione dell'apparecchiatura, l'utente è invitato a provare a correggere l'interferenza da una o più delle misure seguenti:

- Riorientare o rilocare l'antenna ricevente.
- Aumentare la separazione tra apparecchiature e ricevitore.
- Connettere l'apparecchiatura a un Outlet in un circuito diverso da quello a cui è connesso il ricevitore.
- Per assistenza, consultare il rivenditore o un tecnico esperto di radio/TV.

Per ulteriori informazioni sui problemi di interferenza, visitare il sito Web FCC all'indirizzo [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). È anche possibile chiamare FCC a 1-888-CALL FCC per richiedere l'interferenza e le schede dei fatti interferenze telefoniche.

Per informazioni aggiuntive sulla sicurezza della radiofrequenza, vedere il sito Web FCC presso [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) il sito Web di Industry Canada all'indirizzo [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Questo prodotto ha dimostrato la conformità EMC in condizioni che includono l'uso di dispositivi periferici conformi e cavi schermati tra i componenti di sistema. È importante usare dispositivi periferici conformi e cavi schermati tra i componenti di sistema per ridurre la possibilità di causare interferenze a Radio, televisori e altri dispositivi elettronici.

Questo dispositivo è conforme alla parte 15 delle normative regole FCC e agli standard RSS esenti da licenza Industry Canada. L'utilizzo è soggetto alle due condizioni seguenti: (1) il dispositivo non deve causare interferenze dannose e (2) il dispositivo deve accettare eventuali interferenze ricevute, comprese quelle che possono provocare un funzionamento indesiderato del dispositivo.

![Avviso di informazioni normative 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, Stati Uniti

Stati Uniti: (800) 426-9400

Canada: (800) 933-4750

ID FCC dell'adattatore USB per Netgear a6150 WiFi: PY318300429
 
ID IC adattatore USB per Netgear a6150 WiFi: 4054A-18300429

L'adattatore USB Netgear a6150 WiFi fornito con questa apparecchiatura è conforme a SAR per popolazione generale/limiti di esposizione non controllata in IC RSS-102 ed è stato testato in conformità ai metodi e alle procedure di misurazione specificati in IEEE 1528. Mantenere la distanza di almeno 10 mm per la condizione del corpo.

L'adattatore USB Netgear a6150 Wi-Fi è conforme al limite di esposizione RF portabile in Canada stabilito per un ambiente non controllato ed è sicuro per le operazioni desiderate, come descritto nel manuale. Una maggiore riduzione dell'esposizione RF può essere eseguita mantenendo il prodotto il più possibile dal corpo o impostando il dispositivo su una potenza di output inferiore se tale funzione è disponibile.

Una tabella con una velocità di assorbimento specifica (SAR) media oltre 1 g per ogni prodotto può essere visualizzata nella sezione precedente.

![Avviso di informazioni normative 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>UNIONE EUROPEA:

Richiedere una copia della dichiarazione di conformità dell'Unione europea per questo dispositivo. Inviare un messaggio di posta elettronica all'indirizzo [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

L'adattatore USB Netgear a6150 WiFi fornito con questa apparecchiatura è conforme alla direttiva 2014/53/EU e può essere fornito anche su richiesta.

![Avviso icona 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Avviso:**  

Si tratta di un prodotto di Classe A. In un ambiente domestico, questo prodotto può causare interferenze radio nel qual caso l'utente può essere tenuto ad adottare misure adeguate.

Smaltimento delle batterie esauste e delle apparecchiature elettriche ed elettroniche:

![Icona di avviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Questo simbolo sul prodotto, sulle relative batterie o sull'imballaggio indica che il prodotto ed eventuali batterie incluse non devono essere smaltiti con i rifiuti domestici. È responsabilità dell'utente smaltire il prodotto in un punto di raccolta appositamente adibito al riciclaggio di batterie e apparecchiature elettriche ed elettroniche. La raccolta differenziata e il riciclaggio consentono di tutelare le risorse naturali ed evitare potenziali conseguenze negative per la salute e l'ambiente dovute alla possibile presenza di sostanze pericolose nelle batterie e nelle apparecchiature elettriche ed elettroniche che potrebbero essere rilasciate dallo smaltimento inappropriato. Per ulteriori informazioni su come smaltire le batterie e le apparecchiature elettriche ed elettroniche, contattare l'ufficio preposto del proprio comune, il servizio responsabile della raccolta dei rifiuti domestici o il negozio presso cui è stato acquistato il prodotto. Contattare erecycle@microsoft.com per ulteriori informazioni sui RAEE.

Questo prodotto contiene batterie a bottone.

L'adattatore USB Netgear a6150 Wi-Fi fornito con questa apparecchiatura è concepito per essere operato vicino al corpo umano ed è testato per la conformità a una velocità di assorbimento specifica (vedere i valori seguenti). Quando si trasporta il prodotto o lo si usa quando si indossa il corpo, mantenere una distanza di 10mm dal corpo per garantire la conformità ai requisiti di esposizione RF.

**Velocità di assorbimento (SAR) specifica di Netgear a6150:** 0,54 W/kg di tempo medio su 10g di tissue

 
Questo dispositivo può funzionare in tutti gli Stati membri dell'Unione europea. Osservare le normative nazionali e locali in cui viene usato il dispositivo. Questo dispositivo è limitato all'uso interno solo quando operano nell'intervallo di frequenza di 5150-5350 MHz nei paesi seguenti:  

![Paesi UE che richiedono solo uso interno](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

In conformità con l'articolo 10.8 (a) e la 10.8 (b) del rosso, nella tabella seguente vengono fornite informazioni sulle bande di frequenza utilizzate e la potenza massima di trasmissione RF dei prodotti wireless Netgear per la vendita nell'Unione europea:

**Wi-Fi**

| Intervallo di frequenza (MHz) | Canali utilizzati | Potenza massima di trasmissione (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) con TPC <br> 19,9 dBm (97,7 mW) non TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) con TPC <br> 29,6 dBm (490 mW) non TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Numero di telefono: +353 1 295 3826

Numero di fax: +353 1 706 4110

#### <a name="singapore"></a>Singapore:

L'adattatore USB Netgear a6150 WiFi fornito con questa apparecchiatura è conforme agli standard IMDA.


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack mini-R Edge](azure-stack-edge-mini-r-deploy-prep.md)
