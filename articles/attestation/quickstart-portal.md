---
title: Configurare l'attestazione di Azure con portale di Azure
description: Come configurare e configurare un provider di attestazione utilizzando portale di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ab1e6011a1c127c9ac5a2c7652a4bf458372e1e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733938"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Guida introduttiva: configurare l'attestazione di Azure con portale di Azure

Attenersi ai passaggi seguenti per gestire un provider di attestazione utilizzando portale di Azure.

## <a name="attestation-provider"></a>Provider di attestazioni

### <a name="create-an-attestation-provider"></a>Creare un provider di attestazioni

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Per configurare il provider con criteri non firmati

1.  Dal menu portale di Azure o dalla Home page selezionare **Crea una risorsa** .
2.  Nella casella di ricerca immettere **attestazione**
3.  Dall'elenco dei risultati scegliere **Microsoft Azure attestazione**
4.  Nella pagina Microsoft Azure attestazione scegliere **Crea** .
5.  Nella pagina crea provider di attestazione specificare gli input seguenti:
    
    **Sottoscrizione**: scegliere una sottoscrizione
    
    **Gruppo di risorse**: selezionare un gruppo di risorse esistente o scegliere **Crea nuovo** e immettere un nome per il gruppo di risorse
    
    **Nome**: è necessario un nome univoco

    **Località**: scegliere una località 
    
    **File dei certificati di firma del criterio**: non caricare il file dei certificati di firma del criterio per configurare il provider con criteri non firmati 
6.  Dopo aver fornito gli input necessari, fare clic su **Verifica + crea**
7.  Risolvere eventuali problemi di convalida e fare clic su **Crea**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>Per configurare il provider con i criteri firmati

1.  Dal menu portale di Azure o dalla Home page selezionare **Crea una risorsa** .
2.  Nella casella di ricerca immettere **attestazione**
3.  Dall'elenco dei risultati scegliere **Microsoft Azure attestazione**
4.  Nella pagina Microsoft Azure attestazione scegliere **Crea** .
5.  Nella pagina crea provider di attestazione specificare le informazioni seguenti:
    
    a. **Sottoscrizione**: scegliere una sottoscrizione
    
    b. **Gruppo di risorse**: selezionare un gruppo di risorse esistente o scegliere **Crea nuovo** e immettere un nome per il gruppo di risorse
    
    c. **Nome**: è necessario un nome univoco

    d. **Località**: scegliere una località 
    
    e. **File dei certificati di firma del criterio**: per configurare il provider di attestazione con certificati di firma dei criteri, caricare il file certs. Vedere gli esempi [qui](./policy-signer-examples.md) 
6.  Dopo aver fornito gli input necessari, fare clic su **Verifica + crea**
7.  Risolvere eventuali problemi di convalida e fare clic su **Crea**.

### <a name="view-attestation-provider"></a>Visualizza provider di attestazione

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione e selezionarlo

### <a name="delete-attestation-provider"></a>Elimina provider di attestazione

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare la casella di controllo e fare clic su **Elimina**
4.  Digitare yes e fare clic su **Delete** [or]
1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **Elimina** nel menu in alto e fare clic su **Sì**


## <a name="attestation-policy-signers"></a>Firmatari dei criteri di attestazione

### <a name="view-policy-signer-certificates"></a>Visualizzare i certificati del firmatario dei criteri

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri firmatari certificati** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Scarica certificati di firma dei criteri** (il pulsante verrà disabilitato per i provider di attestazione creati senza requisito di firma dei criteri)
6.  Il file di testo scaricato avrà tutti i certificati in un formato JWS.
a.  Verificare il numero di certificati e i certificati scaricati.

### <a name="add-policy-signer-certificate"></a>Aggiungi certificato di firma del criterio

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri firmatari certificati** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Aggiungi** nel menu in alto (il pulsante verrà disabilitato per i provider di attestazione creati senza requisito di firma dei criteri)
6.  Caricare il file del certificato di firma del criterio e fare clic su **Aggiungi**. Vedere gli esempi [qui](./policy-signer-examples.md)

### <a name="delete-policy-signer-certificate"></a>Elimina certificato del firmatario dei criteri

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri firmatari certificati** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Elimina** nel menu in alto (il pulsante verrà disabilitato per i provider di attestazione creati senza requisito di firma dei criteri)
6.  Caricare il file del certificato di firma del criterio e fare clic su **Elimina**. Vedere gli esempi [qui](./policy-signer-examples.md) 

## <a name="attestation-policy"></a>Criteri di attestazione

### <a name="view-attestation-policy"></a>Visualizzare i criteri di attestazione

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Selezionare il **tipo di attestazione** preferito e visualizzare i **criteri correnti**

### <a name="configure-attestation-policy"></a>Configurare i criteri di attestazione

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Quando il provider di attestazione viene creato senza requisito di firma dei criteri

##### <a name="upload-policy-in-jwt-format"></a>Caricare i criteri nel formato JWT

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Configura** nel menu in alto
6.  Quando il provider di attestazione viene creato senza requisito di firma dei criteri, l'utente può caricare un criterio in formato **JWT** o **testo**
7.  Selezionare il **formato del criterio** come **JWT**
8.  Caricare il file dei criteri con il contenuto del criterio in un formato **JWT senza segno** e quindi fare clic su **Salva**. Vedere gli esempi [qui](./policy-examples.md)
    
    Per l'opzione di caricamento file, l'anteprima dei criteri verrà visualizzata in formato testo e l'anteprima criteri non è modificabile.

7.  Fare clic su **Aggiorna** nel menu in alto per visualizzare i criteri configurati

##### <a name="upload-policy-in-text-format"></a>Caricamento dei criteri in formato testo

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Configura** nel menu in alto
6.  Quando il provider di attestazione viene creato senza requisito di firma dei criteri, l'utente può caricare un criterio in formato **JWT** o **testo**
7.  Selezionare il **formato del criterio** come **testo**
8.  Caricare il file dei criteri con il contenuto in formato **testo** o immettere il contenuto dei criteri nell'area di testo e fare clic su **Salva**. Vedere gli esempi [qui](./policy-examples.md)

    Per l'opzione di caricamento file, l'anteprima dei criteri verrà visualizzata in formato testo e l'anteprima criteri non è modificabile.

8.  Fare clic su **Aggiorna** per visualizzare i criteri configurati

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Quando il provider di attestazione viene creato con il requisito di firma dei criteri

##### <a name="upload-policy-in-jwt-format"></a>Caricare i criteri nel formato JWT

1.  Dal menu portale di Azure o dalla Home page selezionare **tutte le risorse**
2.  Nella casella filtro immettere il nome del provider di attestazione
3.  Selezionare il provider di attestazione e passare alla pagina Panoramica
4.  Fare clic su **criteri** nel menu delle risorse a sinistra o nel riquadro inferiore
5.  Fare clic su **Configura** nel menu in alto
6.  Quando il provider di attestazione viene creato con il requisito di firma dei criteri, l'utente può caricare un criterio solo nel **formato JWT firmato**
7.  Il file dei criteri di caricamento è **firmato JWT Format** e fare clic su **Save**. Vedere gli esempi [qui](./policy-examples.md)

    Per l'opzione di caricamento file, l'anteprima dei criteri verrà visualizzata in formato testo e l'anteprima criteri non è modificabile.
    
8.  Fare clic su **Aggiorna** per visualizzare i criteri configurati

