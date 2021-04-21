---
title: Endpoint servizio di rete virtuale per Azure Key Vault
description: Informazioni su come gli endpoint servizio di rete virtuale Azure Key Vault consentono di limitare l'accesso a una rete virtuale specificata, inclusi gli scenari di utilizzo.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 985cbe1128d1dd64fda86ef062750dc5dd068ffe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751734"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Endpoint servizio di rete virtuale per Azure Key Vault

Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Esiste un'importante eccezione a questa limitazione. Se un utente ha acconsentito esplicitamente a usare servizi Microsoft attendibili, le connessioni da tali servizi sono consentire attraverso il firewall. Ad esempio, questi servizi includono Office 365 Exchange Online, Office 365 SharePoint Online, Calcolo di Azure, Azure Resource Manager e Backup di Azure. Questi utenti devono comunque presentare un token di Azure Active Directory valido e devono avere le autorizzazioni, configurate come criteri di accesso, per eseguire l'operazione richiesta. Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenari di utilizzo

È possibile configurare [reti virtuali e firewall di Key Vault](network-security.md) per negare l'accesso al traffico da tutte le reti, incluso il traffico Internet, per impostazione predefinita. È possibile concedere l'accesso al traffico proveniente da reti virtuali specifiche di Azure e intervalli di indirizzi IP Internet pubblici, creando un limite di rete protetta per le applicazioni.

> [!NOTE]
> I firewall di Key Vault e le regole di rete virtuale si applicano solo al [piano dati](security-overview.md#privileged-access) di Key Vault. Key Vault le operazioni del piano di controllo, ad esempio le operazioni di creazione, eliminazione e modifica, l'impostazione di criteri di accesso, l'impostazione di firewall e regole di rete virtuale e la distribuzione di segreti o chiavi tramite i modelli arm, non sono interessate dai firewall e dalle regole di rete virtuale.

Di seguito sono riportati alcuni esempi di uso degli endpoint del servizio:

* Si usa Key Vault per archiviare chiavi di crittografia, segreti dell'applicazione, certificati e si vuole bloccare l'accesso all'insieme di credenziali delle chiavi dalla rete Internet pubblica.
* Si vuole bloccare l'accesso all'insieme di credenziali delle chiavi in modo che solo l'applicazione o un breve elenco di host designati possano connettesi all'insieme di credenziali delle chiavi.
* Si ha un'applicazione in esecuzione nella rete virtuale di Azure e la rete virtuale è bloccata per tutto il traffico in ingresso e in uscita. L'applicazione deve comunque connettersi a Key Vault per recuperare segreti o certificati o usare le chiavi di crittografia.

## <a name="trusted-services"></a>Servizi attendibili

Di seguito è riportato un elenco di servizi attendibili che sono autorizzati ad accedere a un insieme di credenziali delle chiavi se è abilitata l'opzione **Allow trusted services** (Consenti servizi attendibili).

|Servizio attendibile|Scenari di utilizzo supportati|
| --- | --- |
|Servizio di distribuzione di Macchine virtuali di Azure|[Distribuire i certificati alle macchine virtuali da Key Vault gestito dal cliente](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Servizio di distribuzione dei modelli di Azure Resource Manager|[Passare valori protetti durante la distribuzione](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Servizio di crittografia dei volumi di Crittografia dischi di Azure|Consentire l'accesso a BitLocker Key (VM Windows) o DM Passphrase (VM Linux) e la chiave di crittografia della chiave durante la distribuzione della macchina virtuale. In questo modo si abilita [Crittografia dischi di Azure](../../security/fundamentals/encryption-overview.md).|
|Backup di Azure|Consentire il backup e ripristino di segreti e chiavi pertinenti durante il backup delle macchine virtuali di Azure usando [Backup di Azure](../../backup/backup-overview.md).|
|Exchange Online e SharePoint Online|Consentire l'accesso alla chiave cliente per la crittografia del servizio di archiviazione di Azure con [Chiave cliente](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Consentire l'accesso alla chiave del tenant per [Azure Information Protection](/azure/information-protection/what-is-information-protection).|
|Servizio app di Azure|[Distribuire un certificato dell'app Web di Azure con Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Database SQL di Azure|[Transparent Data Encryption con Bring Your Own Key supporto per database SQL di Azure e Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Archiviazione di Azure|[crittografia del servizio di archiviazione le chiavi gestite dal cliente in Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Archivio Azure Data Lake|[Crittografia dei dati in Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) con una chiave gestita dal cliente.|
|Azure Synapse Analytics|[Crittografia dei dati tramite chiavi gestite dal cliente in Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Servizio di analisi veloce, facile e collaborativo basato su Apache Spark](/azure/databricks/scenarios/what-is-azure-databricks)|
|Gestione API di Azure|[Distribuire certificati per l'Custom Domain da Key Vault tramite MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Recuperare le credenziali dell'archivio dati Key Vault da Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Hub eventi di Azure|[Consentire l'accesso a un insieme di credenziali delle chiavi per uno scenario con chiavi gestite dal cliente](../../event-hubs/configure-customer-managed-key.md)|
|Bus di servizio di Azure|[Consentire l'accesso a un insieme di credenziali delle chiavi per uno scenario con chiavi gestite dal cliente](../../service-bus-messaging/configure-customer-managed-key.md)|
|Importazione/Esportazione di Azure| [Usare chiavi gestite dal cliente in Azure Key Vault per il servizio Importazione/Esportazione](../../import-export/storage-import-export-encryption-key-portal.md)
|Registro Azure Container|[Crittografia del Registro di sistema con chiavi gestite dal cliente](../../container-registry/container-registry-customer-managed-keys.md)
|Gateway applicazione di Azure |[Uso Key Vault certificati per i listener abilitati per HTTPS](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> L'utente deve impostare i criteri di accesso pertinenti per Key Vault in modo da consentire ai servizi corrispondenti di ottenere l'accesso a Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- Per istruzioni dettagliate, vedere [Configurare Azure Key Vault firewall e reti virtuali](network-security.md)
- vedere la panoramica [Azure Key Vault sicurezza](security-overview.md)
