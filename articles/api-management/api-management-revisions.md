---
title: Revisioni in Azure API Management | Microsoft Docs
description: Informazioni sul concetto di revisioni in Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812139"
---
# <a name="revisions-in-azure-api-management"></a>Revisioni in Azure API Management

Le revisioni consentono di apportare modifiche alle API in modo controllato e sicuro. Quando si desidera apportare modifiche, creare una nuova revisione. È quindi possibile modificare e testare l'API senza disturbare i consumer dell'API. Quando si è pronti, si rende la revisione corrente. Allo stesso tempo, è possibile facoltativamente pubblicare una voce nel log delle modifiche per mantenere aggiornati i consumer dell'API con le modifiche. Il log delle modifiche viene pubblicato nel portale per sviluppatori.

> [!NOTE]
> Il portale per sviluppatori non è disponibile al livello A consumo.

Con le revisioni è possibile:

- Apportare in modo sicuro modifiche alle definizioni e ai criteri dell'API, senza disturbare l'API di produzione.
- Provare le modifiche prima di pubblicarle.
- Documentare le modifiche apportate, in modo che gli sviluppatori possano comprendere le novità.
- Eseguire il rollback se si verificano problemi.

[Per iniziare a usare le revisioni, seguire la procedura dettagliata.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Accesso a revisioni specifiche

È possibile accedere a ogni revisione dell'API usando un URL in formato speciale. Aggiungere `;rev={revisionNumber}` alla fine dell'URL DELL'API, ma prima della stringa di query, per accedere a una revisione specifica dell'API. Ad esempio, è possibile usare questo URL per accedere alla revisione 3 `customers` dell'API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Per impostazione predefinita, ogni revisione ha le stesse impostazioni di sicurezza della revisione corrente. È possibile modificare deliberatamente i criteri per una revisione specifica se si vuole applicare una sicurezza diversa per ogni revisione. Ad esempio, è possibile aggiungere criteri di filtro [IP](./api-management-access-restriction-policies.md#RestrictCallerIPs) per impedire ai chiamanti esterni di accedere a una revisione ancora in fase di sviluppo.

Una revisione può essere portata offline, il che la rende inaccessibile ai chiamanti anche se tentano di accedere alla revisione tramite il relativo URL. È possibile contrassegnare una revisione come offline usando il portale di Azure. Se si usa PowerShell, è possibile usare il `Set-AzApiManagementApiRevision` cmdlet e impostare `Path` l'argomento su `$null` .

> [!NOTE]
> È consigliabile portare offline le revisioni quando non le si usa per i test.

## <a name="current-revision"></a>Revisione corrente

Una singola revisione può essere impostata come *revisione* corrente. Questa revisione sarà quella usata per tutte le richieste API che non specificano un numero di revisione esplicito nell'URL. È possibile eseguire il rollback a una revisione precedente impostando tale revisione come corrente.

È possibile impostare una revisione come corrente usando il portale di Azure. Se si usa PowerShell, è possibile usare il `New-AzApiManagementApiRelease` cmdlet .

## <a name="revision-descriptions"></a>Descrizioni delle revisioni

Quando si crea una revisione, è possibile impostare una descrizione per scopi di rilevamento personalizzati. Le descrizioni non vengono riprodotte agli utenti dell'API.

Quando si imposta una revisione come corrente, è anche possibile specificare una nota pubblica del log delle modifiche. Il log delle modifiche è incluso nel portale per sviluppatori che gli utenti dell'API possono visualizzare. È possibile modificare la nota del log delle modifiche usando il `Update-AzApiManagementApiRelease` cmdlet di PowerShell.

## <a name="versions-and-revisions"></a>Versioni e revisioni

Le versioni e le revisioni sono caratteristiche distinte. Ogni versione può avere più revisioni, proprio come un'API senza controllo delle versioni. È possibile usare le revisioni senza usare le versioni o il contrario. In genere le versioni vengono usate per separare le versioni dell'API con modifiche di rilievo, mentre le revisioni possono essere usate per modifiche secondarie e non di rilievo a un'API.

Se la revisione presenta modifiche di rilievo o se si vuole trasformare formalmente la revisione in una versione beta/test, è possibile creare una versione da una revisione. Usando il portale di Azure, fare clic su "Crea versione da revisione" nel menu di scelta rapida della revisione nella scheda Revisioni.
