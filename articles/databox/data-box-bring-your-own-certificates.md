---
title: Usare i propri certificati con dispositivi Azure Data Box/Azure Data Box Heavy
description: Come usare i propri certificati per accedere all'interfaccia utente Web locale e all'archiviazione Blog nel dispositivo Data Box o Data Box Heavy.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545174"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Usare i propri certificati con Data Box e dispositivi Data Box Heavy

Durante l'elaborazione degli ordini, i certificati autofirmati vengono generati per accedere all'interfaccia utente Web locale e all'archiviazione BLOB per un dispositivo Data Box o Data Box Heavy. Se si preferisce comunicare con il dispositivo su un canale attendibile, è possibile usare i propri certificati.

Questo articolo descrive come installare i certificati e come ripristinare i certificati predefiniti prima di restituire il dispositivo al Data Center. Fornisce inoltre un riepilogo dei requisiti del certificato.

## <a name="about-certificates"></a>Informazioni sui certificati

Un certificato fornisce un collegamento tra una **chiave pubblica** e un'entità, ad esempio il nome di dominio, che è stato **firmato** (verificato) da terze parti attendibili, ad esempio un' **autorità di certificazione**.  Un certificato fornisce un modo pratico per distribuire chiavi di crittografia pubbliche attendibili. In questo modo, i certificati garantiscono che la comunicazione sia attendibile e si invii informazioni crittografate al server corretto.

Quando il dispositivo Data Box viene configurato inizialmente, i certificati autofirmati vengono generati automaticamente. Facoltativamente, è possibile importare i propri certificati. Se si prevede di usare i propri certificati, è necessario seguire alcune linee guida.

In un dispositivo Data Box o Data Box Heavy vengono usati due tipi di certificati dell'endpoint:

- Certificato di archiviazione BLOB
- Certificato dell'interfaccia utente locale

### <a name="certificate-requirements"></a>Requisiti per i certificati

I certificati devono soddisfare i requisiti seguenti:

- Il certificato dell'endpoint deve essere in `.pfx` formato con una chiave privata che può essere esportata.
- È possibile utilizzare un singolo certificato per ogni endpoint, un certificato multidominio per più endpoint o un certificato di endpoint con caratteri jolly.
- Le proprietà di un certificato dell'endpoint sono simili alle proprietà di un tipico certificato SSL.
- Nel computer client è necessario un certificato corrispondente nel formato DER ( `.cer` estensione del nome di file).
- Dopo aver caricato il certificato dell'interfaccia utente locale, è necessario riavviare il browser e cancellare la cache. Vedere istruzioni specifiche per il browser.
- I certificati devono essere modificati se il nome del dispositivo o il nome di dominio DNS cambiano.
- Usare la tabella seguente per la creazione di certificati endpoint:

  |Tipo |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
  |---------|---------|---------|---------|
  |Interfaccia utente locale| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Archiviazione BLOB|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Certificato singolo a più reti SAN|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Per ulteriori informazioni, vedere [requisiti dei certificati](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Aggiungere certificati al dispositivo

È possibile usare i propri certificati per accedere all'interfaccia utente Web locale e per accedere all'archiviazione BLOB.

> [!IMPORTANT]
> Se il nome del dispositivo o il dominio DNS viene modificato, è necessario creare nuovi certificati. I certificati client e i certificati del dispositivo devono essere aggiornati con il nuovo nome del dispositivo e il dominio DNS.

Per aggiungere il proprio certificato al dispositivo, attenersi alla procedura seguente:

1. Passare a **Manage**  >  **Certificates**.

   **Nome** indica il nome del dispositivo. **Dominio DNS** indica il nome di dominio per il server DNS.

   Nella parte inferiore della schermata sono visualizzati i certificati attualmente in uso. Per un nuovo dispositivo, verranno visualizzati i certificati autofirmati generati durante l'elaborazione degli ordini.

   ![Pagina certificati per un dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Se è necessario modificare il **nome** (nome dispositivo) o il **dominio DNS** (il dominio del server DNS per il dispositivo), eseguire l'operazione ora prima di aggiungere il certificato. Selezionare quindi **Applica**.

   Il certificato deve essere modificato se il nome del dispositivo o il nome di dominio DNS viene modificato.

   ![Applicare un nuovo nome dispositivo e un dominio DNS per un Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Per aggiungere un certificato, selezionare **Aggiungi certificato** per aprire il pannello **Aggiungi certificato** . Quindi selezionare il **tipo di certificato** , ovvero l' **archiviazione BLOB** o l' **interfaccia utente Web locale**.

   ![Pannello Aggiungi certificati nella pagina certificati per un dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Scegliere il file del certificato (nel `.pfx` formato) e immettere la password impostata al momento dell'esportazione del certificato. Selezionare quindi **Validate & Aggiungi**.

   ![Impostazioni per l'aggiunta di un certificato dell'endpoint BLOB a un Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Una volta che il certificato è stato aggiunto correttamente, nella schermata **certificati** viene visualizzata l'identificazione personale del nuovo certificato. Lo stato del certificato è **valido**.

   ![Un nuovo certificato valido aggiunto correttamente](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Per visualizzare i dettagli del certificato, selezionare e fare clic sul nome del certificato. Il certificato scadrà dopo un anno.

   ![Visualizzare i dettagli del certificato per un dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Se è stato modificato il certificato per l'interfaccia utente Web locale, è necessario riavviare il browser e quindi l'interfaccia utente Web locale. Questo passaggio è necessario per evitare eventuali problemi relativi alla cache SSL.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Installare il nuovo certificato nel computer client che si sta usando per accedere all'interfaccia utente Web locale. Per istruzioni, vedere [importare certificati nel client](#import-certificates-to-client), di seguito.


## <a name="import-certificates-to-client"></a>Importa certificati nel client

Dopo aver aggiunto un certificato al dispositivo Data Box, è necessario importare il certificato nel computer client usato per accedere al dispositivo. Il certificato verrà importato nell'archivio Autorità di certificazione radice attendibili per il computer locale.

Per importare un certificato in un client Windows, attenersi alla procedura seguente:

1. In Esplora file fare clic con il pulsante destro del mouse sul file del certificato (con `.cer` formato) e scegliere **Installa certificato**. Questa azione avvia l'Importazione guidata certificati.

    ![Importare il certificato 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. In **percorso archivio** selezionare **computer locale**, quindi fare clic su **Avanti**.

    ![Selezionare computer locale come percorso dell'archivio nell'importazione guidata certificati](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Selezionare **colloca tutti i certificati nel seguente archivio**, selezionare **autorità di certificazione radice attendibile** e quindi fare clic su **Avanti**.

   ![Selezionare l'archivio Autorità di certificazione radice attendibili nell'importazione guidata certificati](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Verificare le impostazioni e fare clic su **fine**. Un messaggio indica che l'importazione è stata completata.

   ![Verificare le impostazioni del certificato e completare l'importazione guidata certificati](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Ripristinare i certificati predefiniti

Prima di ripristinare il dispositivo nel Data Center di Azure, è necessario ripristinare i certificati originali generati durante l'elaborazione dell'ordine.

Per ripristinare i certificati generati durante l'elaborazione degli ordini, attenersi alla seguente procedura:

1. Passare a **Manage**  >  **Certificates** e selezionare **Revert Certificates**.

   Il ripristino dei certificati restituisce l'utilizzo dei certificati autofirmati generati durante l'elaborazione degli ordini. I certificati vengono rimossi dal dispositivo.

   ![Opzione Ripristina certificati in Gestisci certificati per un dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Una volta completata la riesecuzione del certificato, passare a **Arresta o riavvia** e selezionare **Riavvia**. Questo passaggio è necessario per evitare eventuali problemi relativi alla cache SSL.

   ![Arrestare o riavviare l'interfaccia utente Web locale dopo il ripristino dei certificati in un dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Attendere alcuni minuti e quindi accedere di nuovo all'interfaccia utente Web locale.
