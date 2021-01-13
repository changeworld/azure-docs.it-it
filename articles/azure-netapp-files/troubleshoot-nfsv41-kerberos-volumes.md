---
title: Risolvere i problemi del volume Kerberos NFSv 4.1 per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi del volume Kerberos NFSv 4.1 per Azure NetApp Files.
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
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134314"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Risolvere i problemi del volume Kerberos di NFSv 4.1 

Questo articolo descrive le soluzioni per le condizioni di errore che potrebbero essere presenti durante la creazione o la gestione di volumi Kerberos NFSv 4.1. 

## <a name="error-conditions-and-resolutions"></a>Condizioni di errore e risoluzioni

|     Condizioni di errore    |     Soluzioni    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files non supporta l'autenticazione Kerberos per i volumi NFSv3. Kerberos è supportato solo per il protocollo NFSv 4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Configurare Active Directory per l'account NetApp con i campi **IP KDC** e **nome server ad**. Per istruzioni, vedere [configurare il portale di Azure](configure-kerberos-encryption.md#configure-the-azure-portal) . |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files non supporta la conversione di un volume NFSv 4.1 normale nel volume Kerberos NFSv 4.1 e viceversa. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Esempio: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Verificare che i record A/PTR siano configurati correttamente e siano presenti nel Active Directory per il nome del server `smb-test-64d9.contoso.com` . <br> Nel client NFS, se `nslookup` `smb-test-64d9.contoso.com` viene risolto nell'indirizzo IP IP1 (ovvero `10.1.1.68` ), `nslookup` IP1 deve essere risolto in un solo record (ovvero `smb-test-64d9.contoso.com` ). `nslookup` di IP1 non *deve* essere risolto in più nomi. </li>  <li>Impostare AES-256 per l'account del computer NFS di tipo `NFS-<Smb NETBIOS NAME>-<few random characters>` in Active Directory usando PowerShell o l'interfaccia utente. <br> Comandi di esempio: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Verificare che l'ora del software di archiviazione NFS client, AD e Azure NetApp Files sia sincronizzata tra loro e si trovi entro un intervallo di sfasamento di cinque minuti. </li>  <li>Ottenere il ticket Kerberos nel client NFS usando il comando `kinit <administrator>` .</li> <li>Ridurre il nome host del client NFS a meno di 15 caratteri ed eseguire di nuovo il join dell'area di autenticazione. </li><li>Riavviare il client NFS e il `rpcgssd` servizio come indicato di seguito. Il comando può variare a seconda del sistema operativo.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> Riavviare il `rpc-gssd` servizio. <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Il problema potrebbe essere correlato al problema del client NFS. Riavviare il client NFS.    |
|`Hostname lookup failed`   | È necessario creare una zona di ricerca inversa nel server DNS, quindi aggiungere un record PTR del computer host AD nella zona di ricerca inversa. <br> Si supponga, ad esempio, che l'indirizzo IP del computer AD sia `10.1.1.4` , il nome host del computer ad (come rilevato tramite il comando hostname) sia `AD1` e che il nome di dominio sia `contoso.com` . Il record PTR aggiunto alla zona di ricerca inversa deve essere `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Sono disponibili due soluzioni possibili: <br> <ul><li> Questo errore indica che il DNS non è raggiungibile. Il motivo potrebbe essere un indirizzo IP DNS errato o un problema di rete. Controllare l'indirizzo IP DNS immesso nella connessione AD e verificare che l'indirizzo IP sia corretto. </li> <li> Assicurarsi che AD e il volume si trovino nella stessa area e nella stessa VNet. Se si trovano in reti virtuali diversi, assicurarsi che il peering VNet venga stabilito tra i due reti virtuali. </li></ul> |
|La creazione del volume Kerberos NFSv 4.1 ha esito negativo e restituisce un errore simile all'esempio seguente: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |L'IP KDC è errato e il volume Kerberos è stato creato. Aggiornare l'indirizzo IP KDC con un indirizzo corretto. <br> Dopo aver aggiornato l'indirizzo IP KDC, l'errore non verrà completato. È necessario ricreare il volume. |

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files](configure-kerberos-encryption.md)
