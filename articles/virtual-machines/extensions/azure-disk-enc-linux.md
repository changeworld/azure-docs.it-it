---
title: Crittografia dischi di Azure per Linux
description: Consente la distribuzione di Crittografia dischi di Azure per Linux in una macchina virtuale usando un'estensione macchina virtuale.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792382"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Crittografia dischi di Azure per Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta il sottosistema di dm-crypt di Linux per fornire la crittografia completa del disco nelle [distribuzioni di Linux Azure selezionate](../linux/disk-encryption-overview.md).  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi.

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere Crittografia dischi di Azure per le macchine virtuali [Linux,](../linux/disk-encryption-overview.md)in particolare le sezioni seguenti:

- [Macchine virtuali e sistemi operativi supportati](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi delle macchine virtuali](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisiti di rete](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schema dell'estensione

Esistono due versioni dello schema di estensione per Crittografia dischi di Azure (ADE):
- v1.1: uno schema consigliato più recente che non usa Azure Active Directory proprietà (AAD).
- v0.1: schema precedente che richiede Azure Active Directory proprietà (AAD). 

Per selezionare uno schema di destinazione, la proprietà deve essere impostata sulla versione dello `typeHandlerVersion` schema che si vuole usare.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Nessun AAD (scelta consigliata)

Lo schema v1.1 è consigliato e non richiede proprietà Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: con AAD 

Lo schema 0.1 richiede `AADClientID` e `AADClientSecret` o `AADClientCertificate` .

Utilizzo di `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Utilizzo di `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Data |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1, 0.1 | INT |
| (schema 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schema 0.1) AADClientSecret | password | string |
| (schema 0.1) AADClientCertificate | thumbprint | string |
| (facoltativo) (schema 0.1) Passphrase | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (facoltativo- predefinito RSA-OAEP ) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (facoltativo) KeyEncryptionKeyURL | url | string |
| (facoltativo) KekVaultResourceId | url | string |
| (facoltativo) SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione di modelli basata sullo schema v1.1, vedere il modello di avvio rapido di Azure [201-encrypt-running-linux-vm-without-aad.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

Per un esempio di distribuzione di modelli basata sullo schema v0.1, vedere il modello di avvio rapido di Azure [201-encrypt-running-linux-vm.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

>[!WARNING]
> - Se in precedenza è stato usato il servizio Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la VM.
> - Durante la crittografia dei volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile evitare accessi SSH mentre è in corso la crittografia per evitare che eventuali file aperti a cui è necessario accedere durante il processo di crittografia risultino bloccati. Per controllare lo stato di avanzamento, usare il cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) di PowerShell o il comando dell'interfaccia della riga di comando [vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
> - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato. 

>[!NOTE]
> Inoltre, se il parametro è impostato su Tutti, i dischi dati verranno `VolumeType` crittografati solo se sono montati correttamente.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). 

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare a [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni supporto. Per informazioni sull'uso di supporto di Azure, leggere le domande [Microsoft Azure domande frequenti sul supporto tecnico.](https://azure.microsoft.com/support/faq/)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle estensioni della macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).
* Per altre informazioni sui Crittografia dischi di Azure linux, vedere [Macchine virtuali Linux.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)