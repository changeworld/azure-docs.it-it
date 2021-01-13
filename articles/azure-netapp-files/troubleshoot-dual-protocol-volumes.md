---
title: Risolvere i problemi relativi ai volumi a doppio protocollo per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi relativi a doppio protocollo per Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 0ae7e8f745a91e080d12a47271057ed90f9bc835
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134331"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Risolvere i problemi relativi ai volumi con doppio protocollo

Questo articolo descrive le risoluzioni alle condizioni di errore che possono essere presenti durante la creazione o la gestione di volumi a doppio protocollo.

## <a name="error-conditions-and-resolutions"></a>Condizioni di errore e risoluzioni

|     Condizioni di errore    |     Soluzione    |
|-|-|
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `This Active Directory has no Server root CA Certificate` .    |     Se questo errore si verifica quando si crea un volume a doppio protocollo, verificare che il certificato CA radice sia caricato nell'account NetApp.    |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Il record del puntatore (PTR) del computer host Active Directory potrebbe non essere presente nel server DNS. È necessario creare una zona di ricerca inversa nel server DNS, quindi aggiungere un record PTR del computer host AD nella zona di ricerca inversa. <br> Si supponga, ad esempio, che l'indirizzo IP del computer AD sia `1.1.1.1` , il nome host del computer ad (come rilevato tramite il `hostname` comando) sia `AD1` e il nome di dominio sia `contoso.com` .  Il record PTR aggiunto alla zona di ricerca inversa deve essere `1.1.1.1`  ->  `contoso.com` .   |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Questo errore indica che la password di Active Directory non è corretta quando Active Directory viene aggiunto all'account NetApp. Aggiornare la connessione AD con la password corretta e riprovare. |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Questo errore indica che il DNS non è raggiungibile. Il motivo potrebbe essere dovuto al fatto che l'indirizzo IP DNS non è corretto o si è verificato un problema di rete. Controllare l'indirizzo IP DNS immesso nella connessione AD e verificare che l'indirizzo IP sia corretto. <br> Assicurarsi inoltre che l'annuncio e il volume si trovino nella stessa area e nella stessa VNet. Se si trovano in reti virtuali diversi, assicurarsi che il peering VNet venga stabilito tra i due reti virtuali.|
| Errore di autorizzazione negata durante il montaggio di un volume a doppio protocollo. | Un volume con doppio protocollo supporta i protocolli NFS e SMB.  Quando si tenta di accedere al volume montato sul sistema UNIX, il sistema tenta di eseguire il mapping dell'utente UNIX usato a un utente di Windows. Se non viene trovato alcun mapping, si verifica l'errore "autorizzazione negata". <br> Questa situazione si verifica anche quando si usa l'utente "root" per l'accesso. <br> Per evitare il problema "autorizzazione negata", assicurarsi che Windows Active Directory includa `pcuser` prima di accedere al punto di montaggio. Se si aggiunge `pcuser` dopo aver rilevato il problema "autorizzazione negata", attendere 24 ore prima che la voce della cache venga cancellata prima di riprovare ad accedere. |

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)
* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md)
