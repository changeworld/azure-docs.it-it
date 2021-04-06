---
title: Creare un'area di lavoro Log Analytics nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un'area di lavoro Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali nel portale di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 27eac9cefe645087cae43c34cb6503b562fb7c07
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656304"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Log Analytics nel portale di Azure
Usare il menu **Aree di lavoro Log Analytics** per creare un'area di lavoro Log Analytics tramite il portale di Azure. Un'area di lavoro Log Analytics è un ambiente univoco per i dati di log di Monitoraggio di Azure. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica. È necessaria un'area di lavoro Log Analytics se si intende raccogliere dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione
* Computer locali monitorati tramite System Center Operations Manager
* Raccolte di dispositivi da Configuration Manager 
* Dati di diagnostica o di log dall'archiviazione di Azure

Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

*  [Raccogliere dati dalle macchine virtuali di Azure](../vm/quick-collect-azurevm.md) 
*  [Raccogliere dati dal computer Linux ibrido](../vm/quick-collect-linux-computer.md)
*  [Raccogliere dati dal computer Windows ibrido](../vm/quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

![Portale di Azure](media/quick-create-workspace/azure-portal-01.png)
  
Fare clic su **Aggiungi** e quindi specificare i valori per le opzioni seguenti:

   * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   * Per **Gruppo di risorse** scegliere di usare un gruppo di risorse esistente già configurato oppure crearne uno nuovo.  
   * Specificare un nome per la nuova **area di lavoro Log Analytics**, ad esempio *DefaultLAWorkspace*. Questo nome deve essere univoco a livello globale in tutte le sottoscrizioni di Monitoraggio di Azure.
   * Selezionare un' **area** disponibile.  Per altre informazioni, vedere in quali [aree geografiche Log Analytics è disponibile](https://azure.microsoft.com/regions/services/) e cercare Monitoraggio di Azure dal campo **Ricerca di un prodotto**.  


        ![Creare il pannello della risorsa Log Analytics](media/quick-create-workspace/create-workspace.png)  


Fare clic su **Rivedi e crea** per rivedere le impostazioni e quindi su **Crea** per creare l'area di lavoro. Verrà selezionato il piano tariffario predefinito Pagamento in base al consumo, in base al quale non vengono addebitati costi finché non si inizia a raccogliere una quantità di dati sufficiente. Per ulteriori informazioni sugli altri piani tariffari, vedere la pagina relativa ai [Dettagli sui prezzi log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Risoluzione dei problemi
Quando si crea un'area di lavoro eliminata negli ultimi 14 giorni e in [stato di eliminazione temporanea](../logs/delete-workspace.md#soft-delete-behavior), l'operazione potrebbe avere risultati diversi a seconda della configurazione dell'area di lavoro:
1. Se si specificano lo stesso nome dell'area di lavoro, gruppo di risorse, sottoscrizione e area dell'area di lavoro eliminata, l'area di lavoro verrà ripristinata, inclusi i dati, la configurazione e gli agenti connessi.
2. Se si usa lo stesso nome dell'area di lavoro, ma un gruppo di risorse, una sottoscrizione o un'area diversa, si otterrà un errore *Questo nome dell'area di lavoro è già in uso. Provarne un altro*. Per eseguire l'override dell'eliminazione temporanea ed eliminare definitivamente l'area di lavoro e creare una nuova area di lavoro con lo stesso nome, attenersi alla procedura seguente per ripristinare prima l'area di lavoro ed eseguire l'eliminazione permanente:
   - [Recuperare](../logs/delete-workspace.md#recover-workspace) l'area di lavoro
   - [Eliminare definitivamente](../logs/delete-workspace.md#permanent-workspace-delete) l'area di lavoro
   - Creare una nuova area di lavoro usando il nome della stessa area di lavoro

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi. 

* Vedere [monitorare l'integrità dell'area di lavoro log Analytics in monitoraggio di Azure](../logs/monitor-workspace.md) creare regole di avviso per monitorare l'integrità dell'area di lavoro. 
* Vedere [raccogliere i log e le metriche dei servizi di Azure da usare in log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) per abilitare la raccolta di dati dalle risorse di azure con diagnostica di Azure o archiviazione di Azure.
