---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire le applicazioni IoT Central dalla portale di Azure.
services: iot-central
ms.service: iot-central
author: vishwam
ms.author: vishwams
ms.date: 04/17/2021
ms.topic: how-to
manager: philmea
ms.openlocfilehash: ed65e85c7428bf59fe770534e97afdd53564086a
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107601968"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

È possibile usare il [portale di Azure](https://portal.azure.com) per creare e gestire applicazioni IoT Central, in modo analogo alla funzionalità di IoT Central gestione [applicazioni di](https://apps.azureiotcentral.com/myapps).

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Per creare un'applicazione, passare alla pagina [Crea IoT Central'applicazione](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) nel portale di Azure e compilare il modulo.

![Creare IoT Central modulo](media/howto-manage-iot-central-from-portal/image6a.png)

* **Nome risorsa** è un nome univoco che è possibile scegliere per l'IoT Central nel gruppo di risorse di Azure.

* **URL applicazione è** l'URL che è possibile usare per accedere all'applicazione.

* La **località** è l'[area geografica](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vuole creare l'applicazione. Per ottenere prestazioni ottimali, è in genere consigliabile scegliere la località fisicamente più vicina ai dispositivi. Azure IoT Central è attualmente disponibile nelle posizioni seguenti:
    * Asia Pacifico
    * Australia
    * Europa
    * Giappone
    * Regno Unito
    * Stati Uniti

  Dopo aver scelto una località, non è possibile spostare l'applicazione in un'altra località in un secondo momento.

Dopo aver compilato tutti i campi, selezionare **Crea.** Per altre informazioni, consultare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti

Se è già presente un'Azure IoT Central, è possibile eliminarla o spostarla in una sottoscrizione o in un gruppo di risorse diverso nel portale di Azure.

> [!NOTE]
> Le applicazioni create con *il* piano gratuito non richiedono sottoscrizioni di Azure e pertanto non saranno elencate nella sottoscrizione di Azure nella portale di Azure. È possibile visualizzare e gestire le app gratuite solo dal IoT Central portale.

Per iniziare, cercare l'applicazione nella barra di ricerca nella parte superiore della portale di Azure. È anche possibile visualizzare tutte le applicazioni cercando "IoT Central applicazioni" e selezionando il servizio:

![Screenshot che mostra i risultati della ricerca per "IoT Central applicazioni" con il primo servizio selezionato.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Dopo aver selezionato un'applicazione nei risultati della ricerca, il portale di Azure mostra la relativa panoramica. È possibile passare all'applicazione effettiva selezionando l'URL **dell IoT Central appalto:**

![Screenshot che mostra la pagina "Panoramica" con l'url dell IoT Central app web evidenziato.](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione in un gruppo di risorse diverso, selezionare **Modifica** accanto al gruppo di risorse. Nella pagina **Sposta risorse** scegliere il gruppo di risorse in cui si desidera spostare l'applicazione:

![Screenshot che mostra la pagina "Panoramica" con il gruppo di risorse (modifica)" evidenziato.](media/howto-manage-iot-central-from-portal/image4a.png)

Per spostare l'applicazione in una sottoscrizione diversa, selezionare  **Modifica** accanto alla sottoscrizione. Nella pagina **Sposta risorse** scegliere la sottoscrizione in cui si desidera spostare l'applicazione:

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)