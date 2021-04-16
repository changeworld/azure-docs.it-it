---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531635"
---
* **Un indirizzo [IPv4](../articles/virtual-network/public-ip-addresses.md#standard)pubblico dello SKU Standard,** se il client usa l'API versione 2021-01-01-preview o successiva. La risorsa indirizzo IP pubblico è necessaria quando si configura la rete virtuale per l'accesso esterno o interno. Con una rete virtuale interna, l'indirizzo IP pubblico viene usato solo per le operazioni di gestione. Altre informazioni sugli [indirizzi IP di API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * L'indirizzo IP deve essere nella stessa area e nella stessa sottoscrizione dell'istanza API Management e della rete virtuale.

  * Il valore dell'indirizzo IP viene assegnato come indirizzo IPv4 pubblico virtuale dell'istanza API Management in tale area. 

  * Quando si cambia da una rete virtuale esterna a una rete virtuale interna (o viceversa), si modificano le subnet nella rete o si aggiornano le zone di disponibilità per l'istanza di API Management, è necessario configurare un indirizzo IP pubblico diverso. 

  > [!IMPORTANT]
  > Attualmente, l'portale di Azure usa l'anteprima dell'API versione 2021-01-01 durante la creazione o l'aggiornamento di un API Management istanza. È possibile specificare questa versione dell'API usando un modello Azure Resource Manager o l API Management aPI REST. L'interfaccia della riga di comando di Azure Azure PowerShell attualmente supporta la versione API 2020-12-01.
