---
title: "Guida introduttiva: configurare l'attestazione di Azure usando il portale di Azure"
description: In questa Guida introduttiva si apprenderà come configurare e configurare un provider di attestazione usando il portale di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a8ab7195958a6c2d5a730c38232eb348d505deda
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593604"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Guida introduttiva: configurare l'attestazione di Azure usando il portale di Azure

Seguire questa Guida introduttiva per iniziare a usare l'attestazione di Azure. Informazioni su come gestire un provider di attestazione, un firmatario dei criteri e un criterio usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="attestation-provider"></a>Provider di attestazioni

In questa sezione verrà creato un provider di attestazione e configurato con criteri non firmati o criteri firmati. Si apprenderà anche come visualizzare ed eliminare il provider di attestazione.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Creazione e configurazione del provider con criteri non firmati

1. Passare al menu portale di Azure o al home page e selezionare **Crea una risorsa**.
1. Nella casella di ricerca immettere **attestazione**.
1. Nell'elenco dei risultati selezionare **Microsoft Azure attestazione**.
1. Nella pagina **Microsoft Azure attestazione** selezionare **Crea**.
1. Nella pagina **crea provider di attestazione** specificare gli input seguenti:

   - **Sottoscrizione** Scegliere una sottoscrizione.
   - **Gruppo di risorse**: selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
   - **Nome**: immettere un nome univoco.
   - **Località**: scegliere una località.
   - **File dei certificati di firma del criterio**: non caricare il file dei certificati del firmatario dei criteri per configurare il provider con criteri non firmati.

1. Dopo aver fornito gli input obbligatori, selezionare **Verifica + crea**.
1. Se si verificano problemi di convalida, correggerli e quindi selezionare **Crea**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Creare e configurare il provider con i criteri firmati

1. Passare al menu portale di Azure o al home page e selezionare **Crea una risorsa**.
1. Nella casella di ricerca immettere **attestazione**.
1. Nell'elenco dei risultati selezionare **Microsoft Azure attestazione**.
1. Nella pagina **Microsoft Azure attestazione** selezionare **Crea**.
1. Nella pagina **crea provider di attestazione** specificare le informazioni seguenti:

   - **Sottoscrizione** Scegliere una sottoscrizione.
   - **Gruppo di risorse**: selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
   - **Nome**: immettere un nome univoco.
   - **Località**: scegliere una località.
   - **File dei certificati di firma del criterio**: caricare il file dei certificati di firma del criterio per configurare il provider di attestazione con i criteri firmati. [Vedere esempi di certificati del firmatario dei criteri](./policy-signer-examples.md).

1. Dopo aver fornito gli input obbligatori, selezionare **Verifica + crea**.
1. Se si verificano problemi di convalida, correggerli e quindi selezionare **Crea**.

### <a name="view-the-attestation-provider"></a>Visualizzazione del provider di attestazione

1. Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1. Nella casella filtro immettere il nome del provider di attestazione e selezionarlo.

### <a name="delete-the-attestation-provider"></a>Eliminare il provider di attestazione

Esistono due modi per eliminare il provider di attestazione. È possibile:

1. Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1. Nella casella filtro immettere il nome del provider di attestazione.
1. Selezionare la casella di controllo e selezionare **Elimina**.
1. Immettere **Yes** e selezionare **Delete**.

In alternativa, è possibile:

1. Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1. Nella casella filtro immettere il nome del provider di attestazione.
1. Selezionare il provider di attestazione e passare alla pagina panoramica.
1. Selezionare **Elimina** nella barra dei menu e selezionare **Sì**.

## <a name="attestation-policy-signers"></a>Firmatari dei criteri di attestazione

Eseguire la procedura descritta in questa sezione per visualizzare, aggiungere ed eliminare i certificati del firmatario dei criteri.

### <a name="view-the-policy-signer-certificates"></a>Visualizzare i certificati del firmatario dei criteri

1. Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1. Nella casella filtro immettere il nome del provider di attestazione.
1. Selezionare il provider di attestazione e passare alla pagina panoramica.
1. Selezionare **certificati di firma dei criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1. Selezionare **Scarica certificati di firma dei criteri**. Il pulsante verrà disabilitato per i provider di attestazione creati senza il requisito di firma dei criteri.
1. Il file di testo scaricato avrà tutti i certificati in un formato JWS.
1. Verificare il numero di certificati e i certificati scaricati.

### <a name="add-the-policy-signer-certificate"></a>Aggiungere il certificato di firma del criterio

1.  Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1.  Nella casella filtro immettere il nome del provider di attestazione.
1.  Selezionare il provider di attestazione e passare alla pagina panoramica.
1.  Selezionare **certificati di firma dei criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1.  Selezionare **Aggiungi** nel menu superiore. Il pulsante verrà disabilitato per i provider di attestazione creati senza il requisito di firma dei criteri.
1.  Caricare il file del certificato di firma del criterio e selezionare **Aggiungi**. [Vedere esempi di certificati del firmatario dei criteri](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Eliminare i certificati del firmatario dei criteri

1.  Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1.  Nella casella filtro immettere il nome del provider di attestazione.
1.  Selezionare il provider di attestazione e passare alla pagina panoramica.
1.  Selezionare **certificati di firma dei criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1.  Selezionare **Elimina** nel menu superiore. Il pulsante verrà disabilitato per i provider di attestazione creati senza il requisito di firma dei criteri.
1.  Caricare il file del certificato di firma del criterio e selezionare **Elimina**. [Vedere esempi di certificati del firmatario dei criteri](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Criteri di attestazione

In questa sezione viene descritto come visualizzare i criteri di attestazione e come configurare i criteri creati con e senza un requisito di firma dei criteri.

### <a name="view-an-attestation-policy"></a>Visualizzare i criteri di attestazione

1.  Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1.  Nella casella filtro immettere il nome del provider di attestazione.
1.  Selezionare il provider di attestazione e passare alla pagina panoramica.
1.  Selezionare **criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1.  Selezionare il **tipo di attestazione** preferito e visualizzare i **criteri correnti**.

### <a name="configure-an-attestation-policy"></a>Configurare un criterio di attestazione

Seguire questa procedura per caricare un criterio in JWT o in formato testo se il provider di attestazione è stato creato senza un requisito di firma dei criteri.

1. Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1. Nella casella filtro immettere il nome del provider di attestazione.
1. Selezionare il provider di attestazione e passare alla pagina panoramica.
1. Selezionare **criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1. Selezionare **Configura** nel menu superiore.
1. Selezionare il **formato del criterio** come **JWT** o come **testo**.

   Se il provider di attestazione è stato creato senza requisito di firma dei criteri, l'utente può caricare un criterio in formato **JWT** o **testo** .

      - Se si sceglie il formato JWT, caricare il file dei criteri con il contenuto dei criteri in formato **JWT senza segno** e selezionare **Salva**. [Vedere esempi di criteri](./policy-examples.md).
      - Se si sceglie formato testo, caricare il file dei criteri con il contenuto in formato **testo** o immettere il contenuto del criterio nell'area di testo e selezionare **Salva**. [Vedere esempi di criteri](./policy-examples.md).

   Per l'opzione di caricamento dei file, l'anteprima dei criteri viene visualizzata in formato testo e non è modificabile.

1. Selezionare **Aggiorna** nel menu superiore per visualizzare i criteri configurati.

Se il provider di attestazione è stato creato con un requisito di firma dei criteri, attenersi alla procedura seguente per caricare un criterio nel formato JWT.

1.  Passare al menu portale di Azure o al home page e selezionare **tutte le risorse**.
1.  Nella casella filtro immettere il nome del provider di attestazione.
1.  Selezionare il provider di attestazione e passare alla pagina panoramica.
1.  Selezionare **criteri** dal menu delle risorse sul lato sinistro della finestra o nel riquadro inferiore.
1.  Selezionare **Configura** nel menu superiore.
1.  Caricare il file dei criteri nel **formato JWT firmato** e selezionare **Salva**. [Vedere esempi di criteri](./policy-examples.md).

    Se il provider di attestazione è stato creato con un requisito di firma dei criteri, l'utente può caricare un criterio solo nel **formato JWT firmato**.

    Per l'opzione di caricamento dei file, l'anteprima dei criteri viene visualizzata in formato testo e non è modificabile.
    
1.  Selezionare **Aggiorna** per visualizzare i criteri configurati.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Attestare un'enclave SGX con esempi di codice](/samples/browse/?expanded=azure&terms=attestation)

