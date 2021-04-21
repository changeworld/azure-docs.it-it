---
title: Azure Key Vault spostare un insieme di credenziali in un gruppo di risorse diverso | Microsoft Docs
description: Indicazioni sullo spostamento di un insieme di credenziali delle chiavi in un gruppo di risorse diverso.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749565"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Spostamento di Azure Key Vault tra gruppi di risorse

## <a name="overview"></a>Panoramica

Lo spostamento di un insieme di credenziali delle chiavi tra gruppi di risorse è una funzionalità dell'insieme di credenziali delle chiavi supportata. Lo spostamento di un insieme di credenziali delle chiavi tra gruppi di risorse non influisce sulle configurazioni dei criteri di accesso o del firewall dell'insieme di credenziali delle chiavi. Le applicazioni connesse e le entità servizio devono continuare a funzionare come previsto.

> [!IMPORTANT]
> **Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati.**
> Se si usa l'insieme di credenziali delle chiavi con la crittografia del disco per una macchina virtuale, l'insieme di credenziali delle chiavi non può essere spostato in un gruppo di risorse diverso o in una sottoscrizione mentre la crittografia del disco è abilitata. È necessario disabilitare la crittografia del disco prima di spostare l'insieme di credenziali delle chiavi in un nuovo gruppo di risorse o sottoscrizione. 

## <a name="design-considerations"></a>Considerazioni sulla progettazione

L'organizzazione potrebbe aver implementato Criteri di Azure con imposizione o esclusioni a livello di gruppo di risorse. Potrebbe essere presente un set diverso di assegnazioni di criteri nel gruppo di risorse in cui è attualmente presente l'insieme di credenziali delle chiavi e nel gruppo di risorse in cui si sposta l'insieme di credenziali delle chiavi. Un conflitto nei requisiti dei criteri può interrompere le applicazioni.

### <a name="example"></a>Esempio

Si dispone di un'applicazione connessa all'insieme di credenziali delle chiavi che crea certificati validi per due anni. Il gruppo di risorse in cui si sta tentando di spostare l'insieme di credenziali delle chiavi ha un'assegnazione di criteri che blocca la creazione di certificati validi per più di un anno. Dopo lo spostamento dell'insieme di credenziali delle chiavi nel nuovo gruppo di risorse, l'operazione per creare un certificato valido per due anni verrà bloccata da un'assegnazione di criteri di Azure.

### <a name="solution"></a>Soluzione

Assicurarsi di passare alla pagina Criteri di Azure del portale di Azure e controllare le assegnazioni dei criteri per il gruppo di risorse corrente e il gruppo di risorse in cui si sta passando e assicurarsi che non siano presenti corrispondenze.

## <a name="procedure"></a>Procedura

1. Accedere al Portale di Azure
2. Passare a Key Vault
3. Fare clic sulla scheda "Panoramica"
4. Selezionare il pulsante "Sposta"
5. Selezionare "Sposta in un altro gruppo di risorse" dalle opzioni a discesa
6. Selezionare il gruppo di risorse in cui si vuole spostare l'insieme di credenziali delle chiavi
7. Confermare l'avviso relativo al trasferimento delle risorse
8. Scegliere "OK"

Key Vault valuta la validità dello spostamento delle risorse e avvisa l'utente di eventuali errori. Se non vengono rilevati errori, lo spostamento delle risorse verrà completato. 
