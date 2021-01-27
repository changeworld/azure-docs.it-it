---
title: includere file
description: includere file
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919069"
---
Questa sezione descrive le linee guida e i requisiti per l'allocazione degli spazi di indirizzi del client in cui l'unità di scala del gateway VPN da punto a sito dell'hub WAN è maggiore o uguale a 40.

### <a name="background"></a>Sfondo

I gateway VPN da punto a sito nell'hub WAN virtuale vengono distribuiti con più istanze. Ogni istanza di un gateway VPN da punto a sito può supportare fino a 10.000 connessioni utente da punto a sito simultanee. Di conseguenza, per le unità di scala maggiori di 40, la rete WAN virtuale deve distribuire una capacità aggiuntiva che richiede un numero minimo di pool di indirizzi allocati per diverse unità di scala.

Ad esempio, se si sceglie un'unità di scala 100, vengono distribuite 5 istanze per il gateway VPN da punto a sito nell'hub virtuale. Questa distribuzione può supportare 50.000 connessioni simultanee e almeno 5 pool **di** indirizzi distinti.

**Unità di scala disponibili**

| Unità di scala | Numero massimo di client supportati | Numero minimo di pool di indirizzi |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Specifica di pool di indirizzi

Di seguito sono elencate alcune linee guida per la scelta dei pool di indirizzi. Si noti che le assegnazioni di pool di indirizzi VPN da punto a sito vengono eseguite automaticamente dalla rete WAN virtuale.

1. Un'istanza del gateway consente un massimo di 10.000 connessioni simultanee. Ogni pool di indirizzi deve quindi contenere almeno 10.000 indirizzi IP RFC1918 univoci.
1. Più intervalli di pool di indirizzi vengono combinati automaticamente e assegnati a una **singola** istanza del gateway. Questo processo viene eseguito in modo round robin per tutte le istanze del gateway con un numero di indirizzi IP inferiore a 10.000. Ad esempio, un pool con 5.000 di indirizzi può essere combinato automaticamente dalla rete WAN virtuale con un altro pool che dispone di 8.000 indirizzi e viene assegnato a una singola istanza del gateway.
1. Un pool di indirizzi singolo viene assegnato solo a una singola istanza del gateway tramite la rete WAN virtuale.
1. I pool di indirizzi devono essere distinti. Non può essere presente alcuna sovrapposizione tra i pool di indirizzi.

> [!NOTE] 
> Se un pool di indirizzi è associato a un'istanza del gateway in fase di manutenzione, il pool di indirizzi non può essere riassegnato a un'altra istanza.

### <a name="example"></a>Esempio 

Nell'esempio seguente viene descritta una situazione in cui 60 unità di scala supportano fino a 30.000 connessioni, ma i pool di indirizzi allocati generano meno di 30.000 connessioni simultanee.

Il numero totale di connessioni simultanee supportate in questa installazione è 28.192. La prima istanza del gateway supporta gli indirizzi 10.000, la seconda istanza 8.192 le connessioni e la terza istanza supporta anche gli indirizzi 10.000.

| Numero di pool di indirizzi | Pool di indirizzi | Connessioni supportate |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**#1 di Raccomandazione: verificare che #2 pool di indirizzi disponga di almeno 10.000 indirizzi IP distinti. (esempio: 10.13.0.0/15)**

**#2 di Raccomandazione: aggiungere un altro pool di indirizzi. (esempio: pool di indirizzi #4 10.15.0.0/21 con indirizzi 2048). I pool di indirizzi 2 e 4 verranno combinati automaticamente e consentiranno a tale istanza del gateway di supportare 10.000 connessioni simultanee.**
