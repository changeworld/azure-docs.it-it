---
title: Script di PowerShell per Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Informazioni di riferimento per Azure IoT Edge per Linux negli script di Windows PowerShell per la distribuzione, il provisioning e lo stato IoT Edge per Linux in macchine virtuali Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5286362369e0c4881cdf0c56bc13d1d340056be1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562512"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Script di PowerShell per IoT Edge per Linux in Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Informazioni sugli script di PowerShell per la distribuzione, il provisioning e l'ottenimento dello stato del IoT Edge per Linux in una macchina virtuale Windows.

I comandi descritti in questo articolo derivano dal `AzureEFLOW.psm1` file, che si trova nel sistema nella `WindowsPowerShell` directory sotto `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

Il comando **deploy-eFlow** è il metodo di distribuzione principale. Il comando di distribuzione crea la macchina virtuale, effettua il provisioning dei file e distribuisce il modulo dell'agente di IoT Edge. Sebbene nessuno dei parametri sia necessario, è possibile usarli per eseguire il provisioning del dispositivo IoT Edge durante la distribuzione e modificare le impostazioni per la macchina virtuale durante la creazione. Per un elenco completo, usare il comando `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Per il tipo di provisioning, **X509** si riferisce attualmente esclusivamente al provisioning di X509 usando un [servizio Device provisioning in hub Azure](../iot-dps/about-iot-dps.md). Il metodo di provisioning manuale di X509 non è attualmente supportato.

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| eflowVhdxDir | Percorso directory | Directory in cui la distribuzione archivia il file VHDX per la macchina virtuale. |
| provisioningType | **manuale**, **TPM**, **X509** o **simmetrica** |  Definisce il tipo di provisioning che si vuole usare per il dispositivo IoT Edge. |
| devConnString | La stringa di connessione del dispositivo di un dispositivo IoT Edge esistente | Stringa di connessione del dispositivo per il provisioning manuale di un dispositivo IoT Edge (**manuale**). |
| symmKey | Chiave primaria per una registrazione DPS esistente o chiave primaria di un dispositivo di IoT Edge esistente registrato con chiavi simmetriche | Chiave simmetrica per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| Elemento ScopeId | ID ambito per un'istanza di DPS esistente. | ID ambito per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| registrationId | ID di registrazione di un dispositivo IoT Edge esistente | ID registrazione per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| identityCertLocVm | Percorso directory; deve trovarsi in una cartella che può essere di proprietà del `iotedge` servizio | Percorso di destinazione assoluto del certificato di identità nella macchina virtuale per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| identityCertLocWin | Percorso directory | Percorso di origine assoluto del certificato di identità in Windows per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| identityPkLocVm |  | Percorso directory; deve trovarsi in una cartella che può essere di proprietà del `iotedge` servizio | Percorso di destinazione assoluto della chiave privata di identità nella macchina virtuale per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| identityPkLocWin | Percorso directory | Percorso di origine assoluto della chiave privata di identità in Windows per il provisioning di un dispositivo IoT Edge (**X509** o **simmetrica**). |
| vmSizeDefintion | Non più di 30 caratteri | Definizione del numero di core e della RAM disponibile per la macchina virtuale. **Valore predefinito**: Standard_K8S_v1. |
| vmDiskSize | Da 8 GB a 256 GB | Dimensioni massime del disco rigido virtuale a espansione dinamica. **Valore predefinito**: 16 GB. |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |
| vnetType | **Trasparente** o **ICS** | Tipo di Commuter virtuale. **Valore predefinito**: Transparent. |
| vnetName | Non più di 64 caratteri | Nome del Commuter virtuale. **Valore predefinito**: External. |
| enableVtpm | nessuno | **Parametro switch**. Creare la macchina virtuale con TPM abilitato o disabilitato. |
| mobyPackageVersion | Non più di 30 caratteri |  Versione del pacchetto Moby da verificare o installare nella macchina virtuale.  **Valore predefinito:** 19.03.11. |
| iotedgePackageVersion | Non più di 30 caratteri | Versione del pacchetto di IoT Edge da verificare o installare nella macchina virtuale. **Valore predefinito:** 1.1.0. |
| installPackages | nessuno | **Parametro switch**. Quando questa opzione è attivata, lo script tenterà di installare i pacchetti Moby e IoT Edge anziché verificare solo che i pacchetti siano presenti. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

Il comando **Verify-EflowVm** è una funzione esposta per verificare che sia stata creata la IOT Edge per Linux in una macchina virtuale Windows. Accetta solo parametri comuni e restituisce **true** se la macchina virtuale è stata creata e **false** in caso contrario. Per ulteriori informazioni, utilizzare il comando `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

Il comando **provision-EflowVm** aggiunge le informazioni di provisioning per il dispositivo IOT Edge al file di IOT Edge della macchina virtuale `config.yaml` . Il provisioning può essere eseguito anche durante la fase di distribuzione impostando i parametri nel comando **deploy-eFlow** . Per ulteriori informazioni, utilizzare il comando `Get-Help Provision-EflowVm -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |
| provisioningType | **manuale**, **TPM**, **X509** o **simmetrica** |  Definisce il tipo di provisioning che si vuole usare per il dispositivo IoT Edge. |
| devConnString | La stringa di connessione del dispositivo di un dispositivo IoT Edge esistente | Stringa di connessione del dispositivo per il provisioning manuale di un dispositivo IoT Edge (**manuale**). |
| symmKey | Chiave primaria per una registrazione DPS esistente o chiave primaria di un dispositivo di IoT Edge esistente registrato con chiavi simmetriche | Chiave simmetrica per il provisioning di un dispositivo IoT Edge (**DPS**, **simmetrico**). |
| Elemento ScopeId | ID ambito per un'istanza di DPS esistente. | ID ambito per il provisioning di un dispositivo di IoT Edge (**DPS**). |
| registrationId | ID di registrazione di un dispositivo IoT Edge esistente | ID registrazione per il provisioning di un dispositivo di IoT Edge (**DPS**). |
| identityCertLocVm | Percorso directory; deve trovarsi in una cartella che può essere di proprietà del `iotedge` servizio | Percorso di destinazione assoluto del certificato di identità nella macchina virtuale per il provisioning di un dispositivo IoT Edge (**DPS**, **X509**). |
| identityCertLocWin | Percorso directory | Percorso di origine assoluto del certificato di identità in Windows per il provisioning di un dispositivo IoT Edge (**DPS**, **X509**). |
| identityPkLocVm |  | Percorso directory; deve trovarsi in una cartella che può essere di proprietà del `iotedge` servizio | Percorso di destinazione assoluto della chiave privata di identità nella macchina virtuale per il provisioning di un dispositivo IoT Edge (**DPS**, **X509**). |
| identityPkLocWin | Percorso directory | Percorso di origine assoluto della chiave privata di identità in Windows per il provisioning di un dispositivo IoT Edge (**DPS**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

Il comando **Get-EflowVmName** viene usato per eseguire una query sul nome host corrente della macchina virtuale. Questo comando esiste per tenere conto del fatto che il nome host di Windows può cambiare nel tempo. Accetta solo parametri comuni e restituisce il nome host corrente della macchina virtuale. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

Il comando **Get-EflowLogs** viene usato per raccogliere e raggruppare i log dalla IOT Edge per la distribuzione di Linux in Windows. Restituisce i log in bundle sotto forma di `.zip` cartella. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowLogs -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Il comando **Get-EflowVmTpmProvisioningInfo** viene usato per raccogliere e visualizzare le informazioni di provisioning vTPM della macchina virtuale. Se la macchina virtuale è stata creata senza vTPM, il comando restituirà che non è stato possibile trovare le informazioni di provisioning TPM. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Il comando **Get-EflowVmAddr** viene usato per trovare e visualizzare gli indirizzi IP e Mac della macchina virtuale. Accetta solo parametri comuni. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

Il comando **Get-EflowVmSystemInformation** viene usato per raccogliere e visualizzare le informazioni di sistema dalla macchina virtuale, ad esempio l'utilizzo della memoria e dell'archiviazione. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmSystemInformation -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

Il comando **Get-EflowVmEdgeInformation** viene usato per raccogliere e visualizzare IOT Edge informazioni dalla macchina virtuale, ad esempio la versione di IOT Edge la macchina virtuale è in esecuzione. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmEdgeInformation -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

Il comando **Get-EflowVmEdgeModuleList** viene usato per eseguire query e visualizzare l'elenco dei moduli IOT Edge in esecuzione nella macchina virtuale. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

Il comando **Get-EflowVmEdgeStatus** viene usato per eseguire query e visualizzare lo stato di IOT Edge runtime nella macchina virtuale. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmEdgeStatus -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

Il comando **Get-EflowVmUserName** viene usato per eseguire query e visualizzare il nome utente della macchina virtuale usato per creare la macchina virtuale dal registro di sistema. Accetta solo parametri comuni. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

Il comando **Get-EflowVmSshKey** viene usato per eseguire query e visualizzare la chiave SSH usata dalla macchina virtuale. Accetta solo parametri comuni. Per ulteriori informazioni, utilizzare il comando `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Il comando **SSH-EflowVm** viene usato per SSH nella macchina virtuale. L'unico account a cui è consentita la connessione SSH alla macchina virtuale è l'utente che l'ha creata. Per ulteriori informazioni, utilizzare il comando `Get-Help Ssh-EflowVm -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| vmUser | Non più di 30 caratteri | Nome utente per l'accesso alla macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare questi comandi nell'articolo seguente:

* [Installare Azure IoT Edge per Linux in Windows](./how-to-install-iot-edge-windows-on-windows.md)

* Vedere le informazioni di [riferimento sullo script IOT Edge per Linux in Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) per tutti i comandi disponibili tramite PowerShell.