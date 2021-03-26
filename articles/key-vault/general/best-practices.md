---
title: Procedure consigliate per l'uso di Key Vault-Azure Key Vault | Microsoft Docs
description: Informazioni sulle procedure consigliate per Azure Key Vault, incluso il controllo dell'accesso, quando usare insiemi di credenziali delle chiavi separati, backup, registrazione e opzioni di ripristino.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: e81cbd7e6584f4a280ab9507a989b52d3b188f2d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566609"
---
# <a name="best-practices-to-use-key-vault"></a>Procedure consigliate per l'utilizzo di Key Vault

## <a name="use-separate-key-vaults"></a>Usare insiemi di credenziali delle chiavi distinti

Si consiglia di usare un insieme di credenziali per ogni applicazione per ambiente (sviluppo, pre-produzione e produzione). Ciò consente di non condividere i segreti tra gli ambienti e di ridurre anche la minaccia in caso di violazione.

## <a name="control-access-to-your-vault"></a>Controllare l'accesso all'insieme di credenziali

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo [articolo](secure-your-key-vault.md) fornisce una panoramica del modello di accesso key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

I suggerimenti per il controllo dell'accesso all'insieme di credenziali sono i seguenti:
1. Bloccare l'accesso alla sottoscrizione, al gruppo di risorse e agli insiemi di credenziali delle chiavi (RBAC di Azure)
2. Creare criteri di accesso per ogni insieme di credenziali
3. Usare l'entità di accesso con privilegi minimi per concedere l'accesso
4. Attivare gli endpoint di [servizio](overview-vnet-service-endpoints.md) firewall e VNET

## <a name="backup"></a>Backup

Assicurarsi di eseguire backup regolari dell'insieme di credenziali per l'aggiornamento, l'eliminazione o la creazione di oggetti all'interno di un insieme di credenziali.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell comandi di backup

* [Certificato di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Chiave di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Segreto di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Comandi di backup di Azure CLI

* [Certificato di backup](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Chiave di backup](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Segreto di backup](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Attivare la registrazione

[Attivare la registrazione](logging.md) per l'insieme di credenziali. Configurare anche gli avvisi.

## <a name="turn-on-recovery-options"></a>Attiva opzioni di ripristino

1. Attivare l' [eliminazione](soft-delete-overview.md)temporanea.
2. Attivare ripulitura protezione se si vuole evitare l'eliminazione forzata del segreto/insieme di credenziali anche dopo aver attivato l'eliminazione temporanea.