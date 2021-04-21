---
title: Aumento del limite delle connessioni di rete
description: Aumento del limite delle connessioni di rete
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775566"
---
# <a name="networking-limit-increase"></a>Aumento del limite delle connessioni di rete

Usare il [portale di Azure](https://portal.azure.com) per aumentare la quota di rete.

Per visualizzare l'utilizzo e la quota di rete correnti portale di Azure, aprire la sottoscrizione e quindi selezionare **Usages + quotas**(Utilizzi e quote). È anche possibile usare le opzioni seguenti per visualizzare l'utilizzo e i limiti della rete.

* [Interfaccia della riga di comando per l'](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API di utilizzo della rete](/rest/api/virtualnetwork/virtualnetworks/listusage)

È possibile richiedere un aumento usando **Guida e supporto** tecnico o in Utilizzi e **quote** nel portale.

> [!Note]
> Per modificare le dimensioni predefinite dei **prefissi ip pubblici,** selezionare Lunghezza minima prefisso rete **IP** pubblico dall'elenco a discesa.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Richiedere l'aumento della quota di rete a livello di sottoscrizione usando Guida e supporto tecnico

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto usando **Guida e supporto** nella portale di Azure.

1. Accedere a [portale di Azure](https://portal.azure.com)e quindi selezionare  Guida e supporto dal menu portale di Azure oppure cercare e selezionare **Guida e supporto.**

    ![Guida e supporto](./media/networking-quota-request/help-plus-support.png)

1. Selezionare **Nuova richiesta di supporto**.

    ![Nuova richiesta di supporto](./media/networking-quota-request/new-support-request.png)

1. Per **Tipo di problema** scegliere Limiti del servizio e della sottoscrizione **(quote).**

    ![Selezionare i limiti della sottoscrizione dall'elenco a discesa del tipo di problema](./media/networking-quota-request/select-quota-issue-type.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Selezionare la sottoscrizione newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. In **Tipo di quota** selezionare **Rete.** Selezionare **Avanti: Soluzioni**.

    ![Selezionare il tipo di quota](./media/networking-quota-request/select-quota-type-network.png)

1. In **DETTAGLI PROBLEMA** selezionare Specificare i **dettagli** e immettere informazioni aggiuntive per elaborare la richiesta.

    ![Specificare i dettagli](./media/networking-quota-request/provide-details-link.png)

1. Nel pannello **Dettagli quota** selezionare un modello di distribuzione, una posizione e le risorse da includere nella richiesta.

    ![Dettagli quota DM](./media/networking-quota-request/quota-details-network.png)

1. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare la risorsa **dal** menu Risorse o selezionare l'icona ignora "x". Dopo aver immesso la quota per ogni risorsa, selezionare **Salva e continuare** con la creazione della richiesta di supporto.

    ![Nuovi limiti](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Richiedere l'aumento della quota di rete a livello di sottoscrizione usando Utilizzi e quote

Seguire queste istruzioni per creare una richiesta di supporto usando **Utilizzo e quota** nel portale di Azure.

1. In https://portal.azure.com cercare e selezionare **Sottoscrizioni**.

    ![Sottoscrizioni](./media/networking-quota-request/search-for-suscriptions.png)

1. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

    ![Seleziona sottoscrizione](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selezionare **Utilizzo e quote**

    ![Selezionare Utilizzo e quote](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Nell'angolo in alto a destra selezionare **Richiedi aumento**.

    ![Richiedi aumento](./media/networking-quota-request/request-increase-from-subscription.png)

1. Seguire i passaggi a partire dal passaggio 3 in [Richiedere l'aumento](#request-networking-quota-increase-at-subscription-level-using-help--support)della quota di rete a livello di sottoscrizione.

## <a name="about-networking-limits"></a>Informazioni sui limiti di rete

Per altre informazioni sui limiti di rete, vedere la sezione [Rete](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) della pagina dei limiti o le domande frequenti sui limiti di rete.
