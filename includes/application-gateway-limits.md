---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4c7c68bf690097004f2f3310a8d89ce954d7f87f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612924"
---
| Risorsa | Limite | Note |
| --- | --- | --- |
| Gateway applicazione di Azure |1\.000 per sottoscrizione | |
| Configurazioni di IP front-end |2 |1 pubblica e 1 privata |
| Porte front-end |100<sup>1</sup> | |
| Pool di indirizzi back-end |100<sup>1</sup> | |
| Server back-end per pool |1.200 | |
| Listener HTTP |200<sup>1</sup> |Limitato a 100 listener attivi che eseguono il routing del traffico. Listener attivi = numero totale di listener - listener non attivi.<br>Se una configurazione predefinita all'interno di una regola di routing è impostata sull'instradamento del traffico (ad esempio, include un listener, un pool back-end e impostazioni HTTP), anche questa viene conteggiata come listener.|
| Regole di bilanciamento del carico HTTP |100<sup>1</sup> | |
| Back-end impostazioni HTTP |100<sup>1</sup> | |
| Istanze per gateway |SKU V1 - 32<br>SKU V2 - 125 | |
| Certificati SSL |100<sup>1</sup> |1 per listener HTTP |
| Dimensioni massime del certificato SSL |SKU V1 - 10 KB<br>SKU V2 - 16 KB| |
| Certificati di autenticazione |100 | |
| Certificati radice trusted |100 | |
| Valore minimo del timeout della richiesta |1 secondo | |
| Valore massimo del timeout della richiesta |24 ore | |
| Numero di siti |100<sup>1</sup> |1 per listener HTTP |
| Mappe URL per listener |1 | |
| Numero massimo di regole basate sul percorso per mappa di URL|100||
| Configurazioni di reindirizzamento |100<sup>1</sup>| |
| Numero di set di regole di riscrittura |400| |
| Numero di configurazioni di intestazioni o URL per set di regole di riscrittura|40| |
| Numero di condizioni per set di regole di riscrittura|40| |
| Connessioni WebSocket simultanee |Gateway medio: 20.000<br> Gateway di grandi dimensioni: 50.000| |
| Lunghezza massima dell'URL|32 KB| |
| Dimensioni massime delle intestazioni per HTTP/2 |4 KB| |
| Dimensioni massime di caricamento file, Standard |2 GB | |
| Dimensioni massime caricamento file WAF |Gateway WAF medi V1, 100 MB<br>Gateway WAF di grandi dimensioni V1, 500 MB<br>WAF V2, 750 MB| |
| Limite delle dimensioni del corpo WAF, senza file|128 KB||
| Numero massimo di regole personalizzate WAF|100||
| Numero massimo di esclusioni WAF per gateway applicazione|40||

<sup>1</sup> Nel caso di SKU abilitati per WAF, è necessario limitare il numero di risorse a 40.
