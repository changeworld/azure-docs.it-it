---
title: Configurare le condizioni di corrispondenza del set di regole standard/Premium di Azure front door
description: Questo articolo fornisce un elenco delle varie condizioni di corrispondenza disponibili con il set di regole standard/Premium di Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100039"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Condizioni di corrispondenza del set di regole standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questa esercitazione illustra come creare un set di regole con il primo set di regole nel portale di Azure. Nel [set di regole](concept-rule-set.md)standard/Premium di Azure front door, una regola è costituita da zero o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel set di regole standard/Premium di Azure front door.

La prima parte di una regola è costituita da una o più condizioni di corrispondenza. Una regola può contenere fino a 10 condizioni di corrispondenza. Una condizione di corrispondenza identifica tipi specifici di richieste per cui vengono eseguite le azioni definite. Se si usano più condizioni di corrispondenza, verranno raggruppate insieme tramite la logica AND. Per tutte le condizioni di corrispondenza che supportano più valori (come indicato di seguito come "delimitati da spazi"), si presuppone l'operatore "OR".

Ad esempio, è possibile usare una condizione di corrispondenza per:

* Filtrare le richieste in base a un indirizzo IP, un paese o un'area specifica.
* Filtrare le richieste in base alle informazioni dell'intestazione.
* Filtrare le richieste provenienti da dispositivi mobili o desktop.
* Filtrare le richieste dal nome del file di richiesta e dall'estensione del file.
* Filtrare le richieste da URL richiesta, protocollo, percorso, stringa di query, argomenti post e così via.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le condizioni di corrispondenza seguenti sono disponibili per l'uso nel set di regole standard/Premium di Azure front door:

## <a name="device-type"></a>Tipo di dispositivo

Identifica le richieste effettuate da un dispositivo mobile o desktop.  

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | Mobile, Desktop

## <a name="post-argument"></a>Argomento richiesta POST

Identifica le richieste in base agli argomenti definiti per il metodo di richiesta POST usato.

#### <a name="required-fields"></a>Required fields

Nome dell'argomento | Operatore | Valore dell'argomento | Trasformazione maiuscole/minuscole
--------------|----------|----------------|---------------
string | [Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

## <a name="query-string"></a>Stringa di query

Identifica le richieste che contengono un parametro di stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un modello specifico. I parametri di stringa di query, ad esempio **parametro=valore**, nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

#### <a name="required-fields"></a>Required fields

Operatore | Stringa di query | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

## <a name="remote-address"></a>Indirizzo remoto

Identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|-----------------
Corrispondenza geografica | Codice paese
Corrispondenza IP | Indirizzo IP (delimitato da spazi)
Non corrispondenza geografica | Codice paese
Non corrispondenza IP | Indirizzo IP (delimitato da spazi)

#### <a name="key-information"></a>Informazioni chiave

* Usare la notazione CIDR.
* Per più indirizzi IP e blocchi di indirizzi IP, viene utilizzata la logica ' OR '.
    * **Esempio IPv4**: se si aggiungono due indirizzi IP *1.2.3.4* e *10.20.30.40*, la condizione viene confrontata se le richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
    * **Esempio IPv6**: se si aggiungono due indirizzi IP *1:2:3:4:5:6:7:8* e *10:20:30:40:50:60:70:80*, la condizione viene confrontata se le richieste provenienti dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
* La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
    * **Esempio di indirizzo IPv4**: *5.5.5.64/26* corrisponde alle richieste provenienti dagli indirizzi compresi tra 5.5.5.64 e 5.5.5.127.
    * **Esempio di indirizzo IPv6**: *1:2:3:/48* corrisponde a qualsiasi richiesta proveniente dagli indirizzi compresi tra 1:2:3:0:0:0:0:0 e 1:2:3: ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Corpo della richiesta

Identifica le richieste in base a testo specifico visualizzato nel relativo corpo.

#### <a name="required-fields"></a>Required fields

Operatore | Corpo della richiesta | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

## <a name="request-header"></a>Intestazione della richiesta

Identifica le richieste che usano un'intestazione specifica.

#### <a name="required-fields"></a>Required fields

Nome intestazione | Operatore | Valore intestazione | Trasformazione maiuscole/minuscole
------------|----------|--------------|---------------
string | [Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

## <a name="request-method"></a>Metodo richiesta

Identifica le richieste che usano il metodo di richiesta specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Informazioni chiave

Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache in Frontdoor di Azure. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete.

## <a name="request-protocol"></a>Protocollo richiesta

Identifica le richieste che usano il protocollo specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | HTTP, HTTPS

## <a name="request-url"></a>URL richiesta

Identifica le richieste che corrispondono all'URL specificato.

#### <a name="required-fields"></a>Required fields

Operatore | URL richiesta | Trasformazione maiuscole/minuscole
---------|-------------|---------------
[Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

#### <a name="key-information"></a>Informazioni chiave

Quando si usa questa condizione della regola, assicurarsi di includere le informazioni sul protocollo. Ad esempio: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Estensione file della richiesta

Identifica le richieste che includono l'estensione del nome file specificata nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Estensione | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco operatori](#operator-list)  | String, Int | Minuscolo, maiuscolo

#### <a name="key-information"></a>Informazioni chiave

Per l'estensione, non includere un punto iniziale. Ad esempio, usare *html* invece di *.html*.

## <a name="request-file-name"></a>Nome file della richiesta

Identifica le richieste che includono il nome file specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Nome file | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco operatori](#operator-list)| String, Int | Minuscolo, maiuscolo

## <a name="request-path"></a>Percorso della richiesta

Identifica le richieste che includono il percorso specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | valore | Trasformazione maiuscole/minuscole
---------|-------|---------------
[Elenco operatori](#operator-list) | String, Int | Minuscolo, maiuscolo

## <a name="operator-list"></a><a name = "operator-list"></a>Elenco operatori

Per le regole che accettano valori dall'elenco di operatori standard, gli operatori seguenti sono validi:

* Qualsiasi
* Uguale a
* Contiene
* Inizia con
* Finisce con
* Minore di
* Minore o uguale a
* Maggiore di
* Maggiore o uguale a
* Nessuno
* Non contiene
* Non inizia con
* Non termina con
* Non è minore di
* Non è minore o uguale a
* Non è maggiore di
* Non è maggiore o uguale a
* Espressione regolare

Per gli operatori numerici, ad esempio *minore di* e *maggiore o uguale a*, il confronto si basa sulla lunghezza. Il valore nella condizione di corrispondenza deve essere un numero intero che equivale alla lunghezza da confrontare.

## <a name="regular-expression"></a>Espressione regolare

Regex non supporta le operazioni seguenti:

* Backreference e acquisizione di sottoespressioni
* Asserzioni di larghezza zero arbitrarie
* Riferimenti a subroutine e modelli ricorsivi
* Modelli condizionali
* Verbi di controllo backtracking
* Direttiva a byte singolo \c
* Direttiva di corrispondenza nuova riga
* La direttiva \k Start of match Reset
* Callout e codice incorporato
* Raggruppamento atomico e quantificatori possessivi

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [set di regole](concept-rule-set.md).
* Informazioni su come [configurare il primo set di regole](how-to-configure-rule-set.md).
* Altre informazioni sulle [azioni del set di regole](concept-rule-set-actions.md).
