---
title: Codici di errore comuni per il servizio metadati dell'istanza di Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Cenni preliminari sui codici di errore comuni e sui metodi di mitigazione corrispondenti per il servizio metadati dell'istanza di Azure (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e648d71ed3c501c07a2491054b273a13d74cedaf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417627"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Codici di errore: codici di errore comuni quando si usa IMDS per recuperare informazioni sul servizio di bilanciamento del carico

Questo articolo descrive gli errori di distribuzione comuni e illustra come risolvere questi errori durante l'uso del servizio metadati dell'istanza di Azure (IMDS).

## <a name="error-codes"></a>Codici di errore

| Codice di errore | Messaggio di errore | Dettagli e mitigazione |
| --- | ---------- | ----------------- |
| 400 | Manca il parametro obbligatorio " \<ParameterName> ". Correggere la richiesta e riprovare. | Il codice di errore indica un parametro mancante. </br> Per altre informazioni sull'aggiunta del parametro missing, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | Il valore del parametro non è consentito oppure il valore \<ParameterValue> del parametro "" non è consentito per il parametro "ParameterName". Correggere la richiesta e riprovare. | Il codice di errore indica che il formato della richiesta non è configurato correttamente. </br> Per altre informazioni, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) per correggere il corpo della richiesta ed emettere un nuovo tentativo. |
| 400 | Richiesta imprevista. Verificare i parametri della query e riprovare. | Il codice di errore indica che il formato della richiesta non è configurato correttamente. </br> Per altre informazioni, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) per correggere il corpo della richiesta ed emettere un nuovo tentativo. |
| 404 | Non sono stati trovati metadati del servizio di bilanciamento del carico. Verificare se la macchina virtuale usa un servizio di bilanciamento del carico SKU non Basic e riprovare più tardi. | Il codice di errore indica che la macchina virtuale non è associata a un servizio di bilanciamento del carico o che il servizio di bilanciamento del carico è uno SKU Basic anziché standard. </br> Per altre informazioni, vedere [Guida introduttiva: creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali usando il portale di Azure](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) per distribuire un servizio di bilanciamento del carico standard.|
| 404 | API non trovata: percorso = " \<UrlPath> ", metodo = " \<Method> " | Il codice di errore indica un errore di configurazione del percorso. </br> Per altre informazioni, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) per correggere il corpo della richiesta ed emettere un nuovo tentativo.|
| 405 | Il metodo HTTP non è consentito: path = " \<UrlPath> ", Method = " \<Method> " | Il codice di errore indica un verbo HTTP non supportato. </br> Per altre informazioni, vedere [servizio metadati dell'istanza di Azure (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows.md#http-verbs) per i verbi supportati. |
| 429 | Numero eccessivo di richieste | Il codice di errore indica un limite di velocità. </br> Per ulteriori informazioni sulla limitazione della frequenza, vedere [servizio metadati dell'istanza di Azure (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows#rate-limiting).|
| 400 | Il corpo della richiesta è maggiore di MaxBodyLength:... | Il codice di errore indica una richiesta di dimensioni maggiori di MaxBodyLength. </br> Per altre informazioni sulla lunghezza del corpo, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | La lunghezza della chiave del parametro è maggiore di MaxParameterKeyLength:... | Il codice di errore indica una lunghezza della chiave del parametro maggiore di MaxParameterKeyLength. </br> Per altre informazioni sulla lunghezza del corpo, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | La lunghezza del valore del parametro è maggiore di MaxParameterValueLength:... | Il codice di errore indica una lunghezza della chiave del parametro maggiore di MaxParameterValueLength. </br> Per altre informazioni sulla lunghezza del valore, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | La lunghezza del valore dell'intestazione del parametro è maggiore di MaxHeaderValueLength:... | Il codice di errore indica una lunghezza del valore dell'intestazione del parametro maggiore di MaxHeaderValueLength. </br> Per altre informazioni sulla lunghezza del valore, vedere [come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | Load Balancer API dei metadati non è disponibile in questo momento. Riprovare più tardi | Il codice di errore indica che è possibile eseguire il provisioning dell'API. Provare la richiesta in un secondo momento. |
| 404 | /Metadata/LoadBalancer non è attualmente disponibile | Il codice di errore indica che l'API è in corso di abilitazione. Provare la richiesta in un secondo momento. |
| 503 | Servizio interno non disponibile. Riprovare più tardi  | Il codice di errore indica che l'API è temporaneamente non disponibile. Provare la richiesta in un secondo momento. |
|  |  |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [servizio metadati dell'istanza di Azure](/virtual-machines/windows/instance-metadata-service.md)

