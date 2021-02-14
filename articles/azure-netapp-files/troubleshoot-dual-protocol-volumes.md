---
title: Risolvere i problemi relativi ai volumi SMB o Dual Protocol per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi relativi a SMB o a doppio protocollo per Azure NetApp Files.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: 237fb514229f370fcba79133232e80a6a655048f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104417"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>Risolvere i problemi relativi ai volumi SMB o Dual Protocol

Questo articolo descrive le risoluzioni alle condizioni di errore che possono essere presenti durante la creazione o la gestione di volumi a doppio protocollo.

## <a name="errors-for-dual-protocol-volumes"></a>Errori per i volumi a doppio protocollo

|     Condizioni di errore    |     Soluzioni    |
|-|-|
| LDAP su TLS è abilitato e la creazione di volumi a doppio protocollo ha esito negativo con l'errore `This Active Directory has no Server root CA Certificate` .    |     Se questo errore si verifica quando si crea un volume a doppio protocollo, verificare che il certificato CA radice sia caricato nell'account NetApp.    |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Il record del puntatore (PTR) del computer host Active Directory potrebbe non essere presente nel server DNS. È necessario creare una zona di ricerca inversa nel server DNS, quindi aggiungere un record PTR del computer host AD nella zona di ricerca inversa. <br> Si supponga, ad esempio, che l'indirizzo IP del computer AD sia `10.X.X.X` , il nome host del computer ad (come rilevato tramite il `hostname` comando) sia `AD1` e il nome di dominio sia `contoso.com` .  Il record PTR aggiunto alla zona di ricerca inversa deve essere `10.X.X.X`  ->  `contoso.com` .   |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` . |     Questo errore indica che la password di Active Directory non è corretta quando Active Directory viene aggiunto all'account NetApp. Aggiornare la connessione AD con la password corretta e riprovare. |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Questo errore indica che il DNS non è raggiungibile. Il motivo potrebbe essere dovuto al fatto che l'indirizzo IP DNS non è corretto o si è verificato un problema di rete. Controllare l'indirizzo IP DNS immesso nella connessione AD e verificare che l'indirizzo IP sia corretto. <br> Assicurarsi inoltre che l'annuncio e il volume si trovino nella stessa area e nella stessa VNet. Se si trovano in reti virtuali diversi, assicurarsi che il peering VNet venga stabilito tra i due reti virtuali.|
| Errore di autorizzazione negata durante il montaggio di un volume a doppio protocollo. | Un volume con doppio protocollo supporta i protocolli NFS e SMB.  Quando si tenta di accedere al volume montato sul sistema UNIX, il sistema tenta di eseguire il mapping dell'utente UNIX usato a un utente di Windows. Se non viene trovato alcun mapping, si verifica l'errore "autorizzazione negata". <br> Questa situazione si verifica anche quando si usa l'utente "root" per l'accesso. <br> Per evitare il problema "autorizzazione negata", assicurarsi che Windows Active Directory includa `pcuser` prima di accedere al punto di montaggio. Se si aggiunge `pcuser` dopo aver rilevato il problema "autorizzazione negata", attendere 24 ore prima che la voce della cache venga cancellata prima di riprovare ad accedere. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>Errori comuni per i volumi SMB e Dual-Protocol

|     Condizioni di errore    |     Soluzioni    |
|-|-|
| La creazione del volume SMB o Dual Protocol non riesce con l'errore seguente: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | Questo errore indica che il DNS non è raggiungibile. <br> Prendere in considerazione le soluzioni seguenti: <ul><li>Controllare se aggiunge e il volume viene distribuito nella stessa area.</li> <li>Controllare se aggiunge e il volume usa lo stesso VNet. Se usano reti virtuali diversi, assicurarsi che i reti virtuali vengano sottoconnessi tra loro. Vedere [linee guida per la pianificazione di Azure NetApp files rete](azure-netapp-files-network-topologies.md). </li> <li>Il server DNS potrebbe avere gruppi di sicurezza di rete (gruppi) applicati.  Di conseguenza, non consente il flusso del traffico. In questo caso, aprire gruppi al DNS o ad per connettersi a diverse porte. Per i requisiti relativi alle porte, vedere [requisiti per le connessioni Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). </li></ul> <br>Si applicano le stesse soluzioni per aggiungere Azure. L'aggiunta di Azure deve essere distribuita nella stessa area. Il VNet deve trovarsi nella stessa area o peering con il VNet usato dal volume. | 
| La creazione del volume SMB o Dual Protocol non riesce con l'errore seguente: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>Verificare che il nome utente immesso sia corretto. </li> <li>Assicurarsi che l'utente faccia parte del gruppo Administrator con privilegi per la creazione di account computer. </li> <li> Se si usa Azure aggiunge, assicurarsi che l'utente faccia parte del gruppo di Azure AD `Azure AD DC Administrators` . </li></ul> | 
| La creazione del volume SMB o Dual Protocol non riesce con l'errore seguente: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | Verificare che la password immessa per l'aggiunta alla connessione AD sia corretta. |
| La creazione del volume SMB o Dual Protocol non riesce con l'errore seguente: `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | Verificare che il percorso OU specificato per l'aggiunta della connessione AD sia corretto. Se si usa Azure aggiunge, verificare che il percorso dell'unità organizzativa sia `OU=AADDC Computers` . |

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)
* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md)
