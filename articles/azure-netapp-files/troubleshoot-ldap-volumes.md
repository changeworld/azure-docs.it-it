---
title: Risolvere i problemi relativi ai volumi LDAP per Azure NetApp Files | Microsoft Docs
description: Descrive le risoluzioni alle condizioni di errore che potrebbero essere presenti durante la configurazione dei volumi LDAP per Azure NetApp Files.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498987"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Risolvere i problemi del volume LDAP

Questo articolo descrive le soluzioni alle condizioni di errore che potrebbero essere presenti durante la configurazione di volumi LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Errori e risoluzioni per i volumi LDAP

|     Condizioni di errore    |     Soluzioni    |
|-|-|
| Errore durante la creazione di un volume SMB con ldapEnabled come true: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Non è possibile creare un volume SMB con LDAP abilitato. <br> Creare volumi SMB con LDAP disabilitato. |
| Errore durante l'aggiornamento del valore del parametro ldapEnabled per un volume esistente: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Non è possibile modificare l'impostazione dell'opzione LDAP dopo la creazione di un volume. <br> Non aggiornare l'impostazione dell'opzione LDAP in un volume creato. Per informazioni dettagliate, vedere [Configure aggiunge LDAP con gruppi estesi per l'accesso al volume NFS](configure-ldap-extended-groups.md) . |
| Errore durante la creazione di un volume NFS abilitato per LDAP: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Questo errore si verifica perché il DNS non è raggiungibile. <br> <ul><li> Controllare se è stato configurato il sito corretto (ambito del sito) per Azure NetApp Files. </li><li> Il motivo per cui DNS non è raggiungibile potrebbe essere un indirizzo IP DNS errato o problemi di rete. Controllare l'indirizzo IP DNS immesso nella connessione ad per assicurarsi che sia corretto. </li><li> Assicurarsi che AD e il volume si trovino nella stessa area e nello stesso VNet. Se si trovano in reti virtuali diversi, assicurarsi che il peering VNet venga stabilito tra i due reti virtuali.</li></ul> |
| Errore durante la creazione di un volume da uno snapshot: <br> `Aggregate does not exist` | Azure NetApp Files non supporta il provisioning di un nuovo volume abilitato per LDAP da uno snapshot che appartiene a un volume disabilitato LDAP. <br> Provare a creare un nuovo volume disabilitato LDAP dallo snapshot specificato. |

## <a name="next-steps"></a>Passaggi successivi  

* [Configure aggiunge LDAP con gruppi estesi per l'accesso al volume NFS](configure-ldap-extended-groups.md)
* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creazione di un volume a doppio protocollo (NFSv3 e SMB) per Azure NetApp Files](create-volumes-dual-protocol.md)