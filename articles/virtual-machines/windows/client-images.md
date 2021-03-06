---
title: Usare immagini client Windows in Azure
description: Come usare i vantaggi della sottoscrizione di Visual Studio per distribuire Windows 7, Windows 8 o Windows 10 in Azure per scenari di sviluppo/test
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 7879e355573d2b9952728828bd3780094a919fe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869056"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Usare client Windows in Azure per scenari di sviluppo/test
A condizione di disporre di una sottoscrizione appropriata di Visual Studio (in precedenza MSDN), è possibile usare Windows 7, Windows 8 o Windows 10 Enterprise (x64) in Azure per scenari di sviluppo/test. 

Per eseguire Windows 10 in un ambiente di produzione, vedere [come distribuire Windows 10 in Azure con diritti di hosting multi-tenant](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Idoneità della sottoscrizione
I sottoscrittori di Visual Studio attivi (utenti che hanno acquistato una licenza di sottoscrizione di Visual Studio) possono usare immagini client Windows a scopo di sviluppo e test. Le immagini client di Windows possono essere usate su hardware o macchine virtuali di Azure.

Alcune immagini client Windows sono disponibili in Azure Marketplace. I sottoscrittori di Visual Studio all'interno di qualsiasi tipo di offerta possono anche [preparare e creare](prepare-for-upload-vhd-image.md) immagini di Windows 7, Windows 8 o Windows 10 a 64 bit e quindi [caricare in Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Offerte idonee e immagini client
La tabella seguente illustra in dettaglio gli ID offerta idonei per la distribuzione di immagini client Windows tramite Azure Marketplace. Le immagini client Windows sono visibili solo alle offerte seguenti. 

> [!NOTE]
> Le offerte di immagine si trovano in **client Windows** in Azure Marketplace. Utilizzare **client Windows** durante la ricerca di immagini client disponibili per i sottoscrittori di Visual Studio. 

| Nome offerta | Numero offerta | Immagini client disponibili | 
|:--- |:---:|:---:|
| [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sottoscrittori di Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sottoscrittori di Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Visual Studio Premium con MSDN (vantaggio)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sottoscrittori di Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sottoscrittori di Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Controllare la sottoscrizione di Azure
Se non si conosce l'ID offerta, è possibile ottenerlo tramite il portale di Azure.  
- Nella finestra *sottoscrizioni* : ![ Dettagli ID offerta dall'portale di Azure](./media/client-images/offer-id-azure-portal.png) 
- Fare clic su **Fatturazione** e quindi sull'ID sottoscrizione. L'ID offerta viene visualizzato nella finestra *Fatturazione*. 
- È anche possibile visualizzare l'ID offerta dalla [scheda "sottoscrizioni"](https://account.windowsazure.com/Subscriptions) del portale degli account di Azure: ![ Dettagli ID offerta nel portale per gli account di Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passaggi successivi
È ora possibile distribuire le VM usando [PowerShell](quick-create-powershell.md), i [modelli di Resource Manager](ps-template.md) o [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
