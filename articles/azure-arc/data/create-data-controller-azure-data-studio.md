---
title: Crea controller dati in Azure Data Studio
description: Crea controller dati in Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 6e61c8819e7ccd868ec92458cff69c37f9277d80
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029625"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Crea controller dati in Azure Data Studio

È possibile creare un controller dati utilizzando Azure Data Studio tramite la distribuzione guidata e i notebook.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Al momento, è possibile creare un controller di dati usando il metodo descritto in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

- È necessario accedere a un cluster Kubernetes e fare in modo che il file kubeconfig sia configurato in modo da puntare al cluster Kubernetes in cui si vuole eseguire la distribuzione.
- È necessario [installare gli strumenti client](install-client-tools.md) di, tra cui **Azure Data Studio** le estensioni Azure Data Studio denominate **Azure Arc** e **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- È necessario accedere ad Azure in Azure Data Studio.  A tale scopo, digitare CTRL + comando + MAIUSC + P per aprire la finestra del testo del comando e digitare **Azure**.  Scegliere **Azure: accedi**.   Nel pannello che viene visualizzato fare clic sull'icona + in alto a destra per aggiungere un account Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Usare la distribuzione guidata per creare il controller dati di Azure Arc

Seguire questa procedura per creare un controller di dati di Azure ARC usando la distribuzione guidata.

1. In Azure Data Studio fare clic sulla scheda connessioni nel pannello di navigazione sinistro.
1. Fare clic sul pulsante **...** nella parte superiore del pannello connessioni e scegliere **nuova distribuzione...**
1. Nella creazione guidata nuova distribuzione scegliere **controller dati di Azure Arc**, quindi fare clic sul pulsante **Seleziona** nella parte inferiore.
1. Verificare che gli strumenti prerequisiti siano disponibili e che soddisfino le versioni richieste. **Fare clic su Avanti**.
1. Usare il file kubeconfig predefinito o selezionarne un altro.  Fare clic su **Avanti**.
1. Scegliere un contesto del cluster Kubernetes. Fare clic su **Avanti**.
1. Scegliere un profilo di configurazione della distribuzione a seconda del cluster Kubernetes di destinazione. **Fare clic su Avanti**.
1. Se si usa Azure Red Hat OpenShift o Red Hat OpenShift container Platform, applicare vincoli di contesto di sicurezza. Seguire le istruzioni in [applicare un vincolo del contesto di sicurezza per Azure Arc Enabled Data Services in OpenShift](how-to-apply-security-context-constraint.md).

   >[!IMPORTANT]
   >In Azure Red Hat OpenShift o Red Hat OpenShift container Platform è necessario applicare il vincolo del contesto di sicurezza prima di creare il controller di dati.

1. Scegliere la sottoscrizione e il gruppo di risorse desiderati.
1. Selezionare una località di Azure.
   
   Il percorso di Azure selezionato qui è il percorso in Azure in cui verranno archiviati i *metadati* sul controller dati e le istanze di database che gestisce. Il controller di dati e le istanze di database verranno effettivamente creati nel cluster Kubernetes laddove possibile.
   
   Al termine, fare clic su **Avanti**.

1. Immettere un nome per il controller dati e per lo spazio dei nomi in cui verrà creato il controller dati.

    Il controller dati e il nome dello spazio dei nomi verranno usati per creare una risorsa personalizzata nel cluster Kubernetes, in modo che siano conformi alle [convenzioni di denominazione di Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Se lo spazio dei nomi esiste già, verrà usato se lo spazio dei nomi non contiene già altri oggetti Kubernetes-pod e così via.  Se lo spazio dei nomi non esiste, verrà eseguito un tentativo di creare lo spazio dei nomi.  Per creare uno spazio dei nomi in un cluster Kubernetes sono necessari i privilegi di amministratore del cluster Kubernetes.  Se non si dispone dei privilegi di amministratore del cluster Kubernetes, chiedere all'amministratore del cluster Kubernetes di eseguire i primi passaggi dell'articolo [creare un controller di dati usando gli strumenti nativi di Kubernetes](./create-data-controller-using-kubernetes-native-tools.md) che devono essere eseguiti da un amministratore di Kubernetes prima di completare la procedura guidata.


1. Selezionare la classe di archiviazione in cui verrà distribuito il controller dati. 
1.  Immettere un nome utente e una password e confermare la password per l'account utente dell'amministratore del controller di dati. Fare clic su **Avanti**.

1. Esaminare la configurazione della distribuzione.
1. Fare clic su **Distribuisci** per distribuire la configurazione desiderata o lo **script nel notebook** per esaminare le istruzioni di distribuzione o apportare le modifiche necessarie, ad esempio i nomi delle classi di archiviazione o i tipi di servizio. Fare clic su **Esegui tutto** nella parte superiore del notebook.

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome "Arc".  Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire ' Arc ' con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).
