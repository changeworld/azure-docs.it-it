---
title: Procedure consigliate per l'Key Vault - Azure Key Vault | Microsoft Docs
description: Informazioni sulle procedure consigliate per Azure Key Vault, tra cui il controllo dell'accesso, quando usare insiemi di credenziali delle chiavi separati, backup, registrazione e opzioni di ripristino.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 7cfa2059cc03b96db39183cfa5056c9934a02290
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814353"
---
# <a name="best-practices-to-use-key-vault"></a>Procedure consigliate per usare Azure Key Vault

## <a name="use-separate-key-vaults"></a>Usare insiemi di credenziali delle chiavi separati

È consigliabile usare un insieme di credenziali per ogni applicazione per ogni ambiente (sviluppo, pre-produzione e produzione). Ciò consente di non condividere segreti tra ambienti e riduce anche la minaccia in caso di violazione.

## <a name="control-access-to-your-vault"></a>Controllare l'accesso all'insieme di credenziali

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo [articolo](security-features.md) offre una panoramica del modello di Key Vault accesso remoto. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

I suggerimenti durante il controllo dell'accesso all'insieme di credenziali sono i seguenti:
1. Bloccare l'accesso alla sottoscrizione, al gruppo di risorse e agli insiemi di credenziali delle chiavi (Controllo degli accessi in base al ruolo di Azure)
2. Creare criteri di accesso per ogni insieme di credenziali
3. Usare l'entità di accesso con privilegi minimi per concedere l'accesso
4. Attivare gli endpoint di servizio firewall [e di rete virtuale](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Assicurarsi di eseguire backup regolari dell'insieme di credenziali in caso di aggiornamento,eliminazione/creazione di oggetti all'interno di un insieme di credenziali.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell comandi di backup

* [Certificato di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Chiave di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Segreto di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Comandi di backup dell'interfaccia della riga di comando di Azure

* [Certificato di backup](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Chiave di backup](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Segreto di backup](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Attivare la registrazione

[Attivare la registrazione per](logging.md) l'insieme di credenziali. Configurare anche gli avvisi.

## <a name="turn-on-recovery-options"></a>Attivare le opzioni di ripristino

1. Attivare [l'eliminazione soft.](soft-delete-overview.md)
2. Attivare la protezione da ripulitura se si vuole proteggersi dall'eliminazione forzata del segreto o dell'insieme di credenziali anche dopo l'attivazione dell'eliminazione soft.