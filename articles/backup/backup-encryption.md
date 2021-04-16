---
title: Crittografia in Backup di Azure
description: Informazioni su come le funzionalità di Backup di Azure consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: 28d165ccc8a966091a96fc433660899d8eef1595
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518474"
---
# <a name="encryption-in-azure-backup"></a>Crittografia in Backup di Azure

Tutti i dati di cui è stato eseguito il backup vengono crittografati automaticamente quando vengono archiviati nel cloud usando la crittografia Archiviazione di Azure, che consente di soddisfare gli impegni di sicurezza e conformità. Questi dati in stato di inquieto vengono crittografati usando la crittografia AES a 256 bit, una delle crittografie a blocchi più potenti disponibili ed è conforme a FIPS 140-2. Oltre alla crittografia in stato di inquieto, tutti i dati di backup in transito vengono trasferiti tramite HTTPS. Rimane sempre nella rete backbone di Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Livelli di crittografia in Backup di Azure

Backup di Azure la crittografia su due livelli:

- **Crittografia dei dati nell'insieme di credenziali di Servizi di ripristino**
  - **Uso delle chiavi gestite dalla piattaforma:** per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita dall'estremità per abilitare questa crittografia. si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.
  - **Uso delle chiavi gestite dal** cliente: quando si esegue il backup delle macchine virtuali di Azure, è possibile scegliere di crittografare i dati usando chiavi di crittografia di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nel Azure Key Vault per crittografare i backup. La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave DIK (Data Encryption Key) basata su AES 256, che a sua volta è protetta usando le chiavi. In questo modo si ha il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario concedere all'insieme di credenziali di Servizi di ripristino l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso quando necessario. Tuttavia, è necessario abilitare la crittografia usando le chiavi prima di tentare di proteggere gli elementi nell'insieme di credenziali. Fare clic [qui](encryption-at-rest-with-cmk.md) per altre informazioni.
  - **Crittografia a livello di infrastruttura:** oltre a crittografare i dati nell'insieme di credenziali di Servizi di ripristino usando chiavi gestite dal cliente, è anche possibile scegliere di configurare un ulteriore livello di crittografia nell'infrastruttura di archiviazione. Questa crittografia dell'infrastruttura è gestita dalla piattaforma. Insieme alla crittografia in stato di inquieto usando chiavi gestite dal cliente, consente la crittografia a due livelli dei dati di backup. La crittografia dell'infrastruttura può essere configurata solo se si sceglie per la prima volta di usare chiavi proprie per la crittografia in stato di inquieto. La crittografia dell'infrastruttura usa chiavi gestite dalla piattaforma per crittografare i dati.
- **Crittografia specifica del carico di lavoro di cui viene eseguito il backup**  
  - **Backup di macchine** virtuali di Azure: Backup di Azure supporta il backup di macchine virtuali con [](../virtual-machines/disk-encryption.md#customer-managed-keys) dischi crittografati con chiavi gestite dalla [piattaforma,](../virtual-machines/disk-encryption.md#platform-managed-keys)nonché chiavi gestite dal cliente di proprietà e gestite dall'utente. È anche possibile eseguire il backup delle macchine virtuali di Azure con il sistema operativo o i dischi dati crittografati [usando](backup-azure-vms-encryption.md#encryption-support-using-ade)Crittografia dischi di Azure . ADE usa BitLocker per le macchine virtuali Windows e DM-Crypt per le macchine virtuali Linux per eseguire la crittografia in guest.

>[!NOTE]
>La crittografia dell'infrastruttura è attualmente in anteprima limitata ed è disponibile solo nelle aree Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, US Gov Arizona e US GOV Virginia. Se si vuole usare la funzionalità in una di queste aree, compilare questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e inviare un messaggio di posta elettronica all'indirizzo [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
- [Backup di Azure domande frequenti](/backup-azure-backup-faq.yml#encryption) sulla crittografia