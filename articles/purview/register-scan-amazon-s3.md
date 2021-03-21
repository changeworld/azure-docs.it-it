---
title: Come analizzare i bucket di Amazon S3
description: Questa guida illustra i dettagli su come eseguire la scansione dei bucket di Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/07/2021
ms.custom: references_regions
ms.openlocfilehash: ddd5c5de85da5ae8cec9d24d33dfd2bf035b5b34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438729"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Connettore Azure per le competenze per Amazon S3

Questa guida dettagliata illustra come usare Azure per analizzare i dati non strutturati attualmente archiviati nei bucket standard di Amazon S3 e come individuare i tipi di informazioni riservate presenti nei dati. Questa guida dettagliata descrive anche come identificare i bucket di Amazon S3 in cui sono attualmente archiviati i dati per semplificare la protezione delle informazioni e la conformità dei dati.

Per questo servizio, usare le competenze per fornire un account Microsoft con accesso sicuro a AWS, in cui verrà eseguito lo scanner di competenze. Lo scanner di competenze usa questo accesso ai bucket di Amazon S3 per leggere i dati e quindi segnala i risultati di analisi, inclusi solo i metadati e la classificazione, di nuovo in Azure. Usare i report di classificazione e assegnazione di etichette per le competenze per analizzare ed esaminare i risultati dell'analisi dei dati.

Questa guida illustra come aggiungere bucket di Amazon S3 come risorse di competenza e come creare un'analisi per i dati di Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Ambito di competenza per Amazon S3

Il seguente ambito è specifico per la registrazione e l'analisi dei bucket di Amazon S3 come origini dati di competenza.

|Ambito  |Descrizione  |
|---------|---------|
|**Limiti dei dati**     |    Il servizio di analisi delle competenze supporta attualmente l'analisi dei bucket di Amazon S3 per un massimo di 100 GB di dati per ogni tenant.     |
|**Tipi di file**     | Il servizio di analisi delle competenze attualmente supporta i tipi di file seguenti: <br><br>. avro,. csv,. doc,. docm,. docx, dot, JSON, ODP, ODS, ODT, ORC, parquet, PDF, POT, PPS,. ppsx,. ppt,. pptm,. pptx,. PSV,. SSV,. TSV,. txt,. xlc,. xls,. xlsb,. xlsm,. xlsx,. xlt,. XML        |
|**Aree**     | Il connettore di competenza per il servizio Amazon S3 è attualmente distribuito solo nelle aree **AWS Stati Uniti orientali (Ohio)** ed **Europe (Francoforte)** . <br><br>Per altre informazioni, vedere [aree di archiviazione e analisi](#storage-and-scanning-regions).   |
|     |         |

Per ulteriori informazioni, vedere i limiti di competenza documentati in:

- [Gestisci e aumenta le quote per le risorse con Azure competenza](how-to-manage-quotas.md)
- [Origini dati e tipi di file supportati in Azure](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Aree di archiviazione e analisi

La tabella seguente consente di eseguire il mapping delle aree in cui i dati vengono archiviati nell'area in cui verranno analizzati da Azure.

> [!IMPORTANT]
> Ai clienti verranno addebitati tutti gli addebiti per il trasferimento dati correlati in base all'area del relativo bucket.
>

| Area di archiviazione | Area di analisi |
| ------------------------------- | ------------------------------------- |
| Stati Uniti orientali (Ohio)                  | Stati Uniti orientali (Ohio)                        |
| Stati Uniti orientali (N. Virginia           | Stati Uniti orientali (Ohio)                        |
| Stati Uniti occidentali (N. California         | Stati Uniti orientali (Ohio)                        |
| Stati Uniti occidentali (Oregon)                | Stati Uniti orientali (Ohio)                        |
| Africa (città del Capo)              | Europa (Francoforte)                    |
| Asia Pacifico (Hong Kong)        | Europa (Francoforte)                    |
| Asia Pacifico (Mumbai)           | Europa (Francoforte)                    |
| Asia Pacifico (Osaka-local)      | Europa (Francoforte)                    |
| Asia Pacifico (Seoul)            | Europa (Francoforte)                    |
| Asia Pacifico (Singapore)        | Europa (Francoforte)                    |
| Asia Pacifico (Sydney)           | Europa (Francoforte)                    |
| Asia Pacifico (Tokyo)            | Europa (Francoforte)                    |
| Canada (centrale)                | Stati Uniti orientali (Ohio)                        |
| Cina (Pechino)                 | Non supportato                    |
| Cina (Ningxia)                 | Non supportato                   |
| Europa (Francoforte)              | Europa (Francoforte)                    |
| Europa (Irlanda)                | Europa (Francoforte)                    |
| Europa (Londra)                 | Europa (Francoforte)                    |
| Europa (Milano)                  | Europa (Francoforte)                    |
| Europa (Parigi)                  | Europa (Francoforte)                    |
| Europa (Stoccolma)              | Europa (Francoforte)                    |
| Medio Oriente (Bahrain)           | Europa (Francoforte)                    |
| America del Sud (São Paulo)       | Stati Uniti orientali (Ohio)                        |
| | |
## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver eseguito i prerequisiti seguenti prima di aggiungere i bucket di Amazon S3 come origini dati di competenza e di analizzare i dati S3.

- È necessario essere un amministratore dell'origine dati di competenza di Azure.

- Quando si aggiungono i bucket come risorse di competenza, saranno necessari i valori di [AWS ARN](#retrieve-your-new-role-arn), il [nome del bucket](#retrieve-your-amazon-s3-bucket-name)e talvolta l'ID dell' [account AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Creare un account di competenza

- **Se si dispone già di un account di competenza,** è possibile continuare con le configurazioni necessarie per il supporto di AWS s3. Iniziare con [la creazione di una credenziale di competenza per l'analisi bucket di AWS](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Se è necessario creare un account di competenza, seguire le istruzioni riportate** in [creare un'istanza di account di Azure](create-catalog-portal.md)per le competenze. Dopo aver creato l'account, tornare qui per completare la configurazione e iniziare a usare il connettore di competenza per Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Creare una credenziale di competenza per l'analisi del bucket di AWS

Questa procedura descrive come creare una nuova credenziale di competenza da usare durante l'analisi dei bucket di AWS.

> [!TIP]
> È anche possibile creare una nuova credenziale nel corso del processo, durante la [configurazione dell'analisi](#create-a-scan-for-your-amazon-s3-bucket). In tal caso, nel campo **Credential (credenziali** ) selezionare **New (nuovo**).
>

1. In ambito, passare al **centro di gestione** e in **sicurezza e accesso** Selezionare **credenziali**.

1. Selezionare **nuovo** e nel riquadro **nuovo credenziali** visualizzato a destra usare i campi seguenti per creare le credenziali di competenza:

    |Campo |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo per la credenziale oppure utilizzare il valore predefinito.        |
    |**Descrizione**     |Immettere una descrizione facoltativa per questa credenziale, ad esempio `Used to scan the tutorial S3 buckets`         |
    |**Metodo di autenticazione**     |Selezionare **Role ARN**, perché si sta usando un ruolo ARN per accedere al bucket.         |
    |**ID account Microsoft**     |Fare clic per copiare questo valore negli Appunti. Usare questo valore come **ID account Microsoft** quando si [Crea il ruolo ARN in AWS](#create-a-new-aws-role-for-purview).           |
    |**ID esterno**     |Fare clic per copiare questo valore negli Appunti. Usare questo valore come **ID esterno** quando si [Crea il ruolo ARN in AWS.](#create-a-new-aws-role-for-purview)        |
    |**ARN del ruolo**     | Dopo aver [creato il ruolo Amazon IAM](#create-a-new-aws-role-for-purview), passare al ruolo nell'area IAM, copiare il valore di **Role ARN** e immetterlo qui. Ad esempio: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Per altre informazioni, vedere [recuperare il nuovo ruolo ARN](#retrieve-your-new-role-arn). |
    | | |

    Selezionare **Crea** al termine della creazione delle credenziali.

Per altre informazioni sulle credenziali di competenza, vedere la [documentazione di anteprima pubblica di Azure](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Creare un nuovo ruolo AWS per la competenza

1.  Aprire la console di **Amazon Web Services** e in **sicurezza, identità e conformità** Selezionare **IAM**.

1. Selezionare **ruoli** e quindi **Crea ruolo**.

1. Selezionare **un altro account AWS**, quindi immettere i valori seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**ID account**     |    Immettere l'ID dell'account Microsoft. ad esempio `615019938638`     |
    |**ID esterno**     |   In Opzioni selezionare **Richiedi ID esterno**, quindi immettere l'ID esterno nel campo designato. <br>ad esempio `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Questo ID esterno è reperibile.  |
    | | |

    > [!NOTE]
    > È possibile trovare i valori per l' **ID dell'account Microsoft** e l' **ID esterno** nell'area relativa alle credenziali del **centro di gestione**  >   , in cui sono state [create le credenziali di competenza](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Ad esempio:

    ![Aggiungere l'ID dell'account Microsoft all'account AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Nell'area **creazione di un ruolo > associazione dei criteri di autorizzazione** filtrare le autorizzazioni visualizzate in **S3**. Selezionare **AmazonS3ReadOnlyAccess** e quindi fare clic su **Avanti: Tag**.

    ![Selezionare il criterio ReadOnlyAccess per il nuovo ruolo di analisi Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. Nell'area **Aggiungi tag (facoltativo)** è possibile scegliere di creare un tag significativo per il nuovo ruolo. I tag utili consentono di organizzare, tenere traccia e controllare l'accesso per ogni ruolo creato dall'utente.

    Immettere una nuova chiave e un valore per il tag in base alle esigenze. Al termine dell'operazione, o se si vuole ignorare questo passaggio, fare clic su **Avanti: rivedere** per esaminare i dettagli del ruolo e completare la creazione del ruolo.

    ![Aggiungere un tag significativo per organizzare, tenere traccia o controllare l'accesso per il nuovo ruolo.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Nell'area **Verifica** eseguire le operazioni seguenti:

    - Nel campo **nome ruolo** immettere un nome significativo per il ruolo
    - Nella casella **Descrizione ruolo** immettere una descrizione facoltativa per identificare lo scopo del ruolo
    - Nella sezione **criteri** , verificare che il criterio corretto (**AmazonS3ReadOnlyAccess**) sia collegato al ruolo.

    Selezionare quindi **Crea ruolo** per completare il processo.

    Ad esempio:

    ![Esaminare i dettagli prima di creare il ruolo.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Configurare l'analisi per i bucket di Amazon S3 crittografati

I bucket AWS supportano più tipi di crittografia. Per i bucket che usano la crittografia **AWS-KMS** , è necessaria una configurazione speciale per abilitare l'analisi.

> [!NOTE]
> Per i bucket che usano nessuna crittografia, AES-256 o la crittografia AWS-KMS S3, ignorare questa sezione e continuare a [recuperare il nome del bucket Amazon S3](#retrieve-your-amazon-s3-bucket-name).
>

**Per controllare il tipo di crittografia usato nei bucket di Amazon S3:**

1. In AWS passare ad **archiviazione**  >  **S3** > e selezionare **bucket** dal menu a sinistra.

    ![Selezionare la scheda bucket Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selezionare il bucket che si desidera controllare. Nella pagina dei dettagli del bucket selezionare la scheda **Proprietà** e scorrere verso il basso fino all'area di **crittografia predefinita** .

    - Se il bucket selezionato è configurato per qualsiasi elemento, ma la crittografia **AWS-KMS** , incluso se la crittografia predefinita per il bucket è **disabilitata**, ignorare il resto di questa procedura e continuare con il [recupero del nome del bucket Amazon S3](#retrieve-your-amazon-s3-bucket-name).

    - Se il bucket selezionato è configurato per la crittografia **AWS-KMS** , continuare come descritto di seguito per aggiungere un nuovo criterio che consente di analizzare un bucket con la crittografia **AWS-KMS** personalizzata.

    Ad esempio:

    ![Visualizzare un bucket Amazon S3 configurato con la crittografia AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Per aggiungere un nuovo criterio per consentire l'analisi di un bucket con la crittografia AWS-KMS personalizzata:**

1. In AWS passare a **Servizi**  >   **IAM**  >   **criteri** e selezionare **Crea criterio**.

1. Nella scheda **Crea**  >  **Editor Visual** Policy definire i criteri con i valori seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Servizio**     |  Immettere e selezionare **KMS**.       |
    |**Actions**     | In **livello di accesso** selezionare **Scrivi** per espandere la sezione **scrittura** .<br>Una volta espansa, selezionare solo l'opzione di **decrittografia** .        |
    |**Risorse**     |Selezionare una risorsa specifica o **tutte le risorse**.         |
    | | |

    Al termine, selezionare **Controlla criteri** per continuare.

    ![Creare un criterio per la scansione di un bucket con la crittografia AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Nella pagina **Verifica criteri** immettere un nome significativo per il criterio e una descrizione facoltativa, quindi selezionare **Crea criterio**.

    I criteri appena creati vengono aggiunti all'elenco dei criteri.

1. Alleghi i nuovi criteri al ruolo aggiunto per l'analisi.

    1. Tornare alla pagina ruoli **IAM**  >   e selezionare il ruolo aggiunto in [precedenza](#create-a-new-aws-role-for-purview).

    1. Nella scheda **autorizzazioni** selezionare **Connetti criteri**.

        ![Nella scheda autorizzazioni del ruolo selezionare Connetti criteri.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Nella pagina **Connetti autorizzazioni** cercare e selezionare il nuovo criterio creato in precedenza. Selezionare **Connetti criterio** per alleghiare i criteri al ruolo.

        La pagina **Riepilogo** viene aggiornata con il nuovo criterio associato al ruolo.

        ![Consente di visualizzare una pagina di riepilogo aggiornata con il nuovo criterio associato al ruolo.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Recuperare il nuovo ruolo ARN

Dovrai registrare il tuo ruolo AWS e copiarlo in ambito durante la creazione di [un'analisi per il bucket di Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

**Per recuperare il ruolo ARN:**

1. Nell'area ruoli di **gestione delle identità e degli accessi (IAM)** di AWS  >   cercare e selezionare il nuovo ruolo [creato per competenza](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Nella pagina **Riepilogo** del ruolo selezionare il pulsante **copia negli Appunti** a destra del valore **ARN del ruolo** .

    ![Copiare il valore di Role ARN negli Appunti.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Incollare questo valore in una posizione sicura, pronto per l'uso quando si [Crea un'analisi per il bucket Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Recuperare il nome del bucket Amazon S3

È necessario il nome del bucket Amazon S3 per copiarlo in ambito durante la creazione di [un'analisi per il bucket Amazon S3](#create-a-scan-for-your-amazon-s3-bucket)

**Per recuperare il nome del bucket:**

1. In AWS passare ad **archiviazione**  >  **S3** > e selezionare **bucket** dal menu a sinistra.

    ![Visualizzare la scheda bucket Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Cercare e selezionare il bucket per visualizzare la pagina dei dettagli del bucket, quindi copiare il nome del bucket negli Appunti.

    Ad esempio:

    ![Recuperare e copiare l'URL del bucket S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Incollare il nome del bucket in un file protetto e aggiungervi un `s3://` prefisso per creare il valore che è necessario immettere quando si configura il bucket come risorsa di competenza.

    ad esempio `s3://purview-tutorial-bucket`

> [!NOTE]
> Come origine dati di competenza è supportato solo il livello radice del bucket. Ad esempio, l'URL seguente, che include una sottocartella, *non* è supportato: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Individuare l'ID dell'account AWS

È necessario l'ID dell'account AWS per registrare l'account AWS come origine dati di competenza, insieme a tutti i relativi bucket.

L'ID dell'account AWS è l'ID usato per accedere alla console di AWS. È possibile trovarlo anche dopo aver eseguito l'accesso al dashboard IAM, a sinistra sotto le opzioni di navigazione e in alto, come parte numerica dell'URL di accesso:

Ad esempio:

![Recuperare l'ID dell'account AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Aggiungere un singolo bucket Amazon S3 come risorsa di competenza

Utilizzare questa procedura se si dispone di un solo bucket S3 che si desidera registrare come origine dati o se si dispone di più bucket nell'account di AWS, ma non si desidera registrarli tutti nella propria competenza.

1. Avviare il portale di competenza usando il connettore di competenza dedicato per Amazon S3 URL. Questo URL è stato fornito dal team di gestione del prodotto del connettore Amazon S3 per la competenza.

    ![Avviare il portale di competenza.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Passare alla pagina delle **origini** di Azure e selezionare l'icona **registra** ![ registro.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Continua**.

    ![Aggiungere un bucket Amazon AWS come origine dati di competenza.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Se si dispone di più [raccolte](manage-data-sources.md#manage-collections) e si desidera aggiungere Amazon S3 a una raccolta specifica, selezionare la **vista mappa** in alto a destra e quindi selezionare l'icona **registra** ![ registro.](./media/register-scan-amazon-s3/register-button.png) pulsante all'interno della raccolta.
    >

1. Nel riquadro **registra origini (Amazon S3)** visualizzato immettere i dettagli seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo oppure utilizzare il valore predefinito fornito.         |
    |**URL bucket**     | Immettere l'URL del bucket di AWS, usando la sintassi seguente:   `s3://<bucketName>`     <br><br>**Nota**: assicurarsi di usare solo il livello radice del bucket, senza sottocartelle. Per altre informazioni, vedere [recuperare il nome del bucket Amazon S3](#retrieve-your-amazon-s3-bucket-name). |
    |**Selezionare una raccolta** |Se si è scelto di registrare un'origine dati all'interno di una raccolta, tale raccolta è già elencata. <br><br>Selezionare una raccolta diversa in base alle esigenze, **Nessuna** per assegnare una raccolta o **nuova** per creare subito una nuova raccolta. <br><br>Per altre informazioni sulle raccolte di competenze, vedere [gestire le origini dati in Azure](manage-data-sources.md#manage-collections).|
    | | |

    Al termine, selezionare **fine** per completare la registrazione.

Continuare con [creare un'analisi per il bucket Amazon S3.](#create-a-scan-for-your-amazon-s3-bucket)

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Aggiungi tutti i bucket di Amazon S3 come risorse di competenza

Utilizzare questa procedura se si dispone di più bucket S3 nell'account Amazon e si desidera registrare tutte le origini dati di competenza.

1. Avviare il portale di competenza usando il connettore di competenza dedicato per Amazon S3 URL. Questo URL è stato fornito dal team di gestione del prodotto del connettore Amazon S3 per la competenza.

    ![Avviare il connettore per Amazon S3 portale di competenza dedicato](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Passare alla pagina delle **origini** di Azure e selezionare l'icona **registra** ![ registro.](./media/register-scan-amazon-s3/register-button.png) > **Account Amazon**  >  **Continua**.

    ![Aggiungere un account Amazon come origine dati di competenza.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Se si dispone di più [raccolte](manage-data-sources.md#manage-collections) e si desidera aggiungere Amazon S3 a una raccolta specifica, selezionare la **vista mappa** in alto a destra e quindi selezionare l'icona **registra** ![ registro.](./media/register-scan-amazon-s3/register-button.png) pulsante all'interno della raccolta.
    >

1. Nel riquadro **registra origini (Amazon S3)** visualizzato immettere i dettagli seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo oppure utilizzare il valore predefinito fornito.         |
    |**ID account AWS**     | Immettere l'ID dell'account AWS. Per altre informazioni, vedere [individuare l'ID dell'account AWS](#locate-your-aws-account-id)|
    |**Selezionare una raccolta** |Se si è scelto di registrare un'origine dati all'interno di una raccolta, tale raccolta è già elencata. <br><br>Selezionare una raccolta diversa in base alle esigenze, **Nessuna** per assegnare una raccolta o **nuova** per creare subito una nuova raccolta. <br><br>Per altre informazioni sulle raccolte di competenze, vedere [gestire le origini dati in Azure](manage-data-sources.md#manage-collections).|
    | | |

    Al termine, selezionare **fine** per completare la registrazione.

Continuare con [creare un'analisi per il bucket Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Creare un'analisi per il bucket Amazon S3

Una volta aggiunti i bucket come origini dati di competenza, è possibile configurare un'analisi per l'esecuzione a intervalli pianificati o immediatamente.

1. Passare all'area **origini** di Azure, quindi eseguire una delle operazioni seguenti:

    - Nella **vista mappa** selezionare **nuova** ![ icona analizza nuova analisi.](./media/register-scan-amazon-s3/new-scan-button.png) nella casella origine dati.
    - Nella **visualizzazione elenco** passare il puntatore del mouse sulla riga dell'origine dati e selezionare **nuova** ![ icona analizza nuova analisi. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. Nel riquadro **Scan..** . che si apre a destra, definire i campi seguenti e quindi selezionare **continue (continua**):

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |  Immettere un nome significativo per l'analisi o usare il valore predefinito.       |
    |**Tipo** |Visualizzato solo se è stato aggiunto l'account AWS, con tutti i bucket inclusi. <br><br>Le opzioni correnti includono solo **tutte le**  >  **Amazzoni S3**. Rimanere sintonizzati per ottenere altre opzioni da selezionare perché la matrice di supporto di di competenza si espande. |
    |**Credenziali**     |  Selezionare una credenziale di competenza con l'ARN del ruolo. <br><br>**Suggerimento**: se si vuole creare una nuova credenziale in questo momento, selezionare **nuovo**. Per altre informazioni, vedere [creare credenziali di competenza per l'analisi del bucket di AWS](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    |     |         |

    La competenza verifica automaticamente che l'ARN del ruolo sia valido e che il bucket e l'oggetto all'interno del bucket siano accessibili e continuino se la connessione ha esito positivo.

    > [!TIP]
    > Per immettere valori diversi e testare manualmente la connessione prima di continuare, selezionare **Test connessione** nella parte inferiore destra prima di selezionare **continua**.
    >

1. Nel riquadro **selezionare un set di regole di analisi** selezionare il set di regole predefinito **AmazonS3** oppure selezionare **nuovo set di regole di analisi** per creare un nuovo set di regole personalizzate. Dopo aver selezionato il set di regole, selezionare **continue (continua**).

    Se si sceglie di creare un nuovo set di regole di analisi personalizzata, utilizzare la procedura guidata per definire le impostazioni seguenti:

    |Riquadro  |Descrizione  |
    |---------|---------|
    |**Nuovo set di regole di analisi** /<br>**Descrizione della regola di analisi**    |   Immettere un nome significativo e una descrizione facoltativa per il set di regole      |
    |**Selezione tipi di file**     | Selezionare tutti i tipi di file che si desidera includere nell'analisi, quindi selezionare **continua**.<br><br>Per aggiungere un nuovo tipo di file, selezionare **nuovo tipo di file** e definire quanto segue: <br>-L'estensione di file da aggiungere <br>-Descrizione facoltativa  <br>-Indica se il contenuto del file include un delimitatore personalizzato o un tipo di file di sistema. Quindi, immettere il delimitatore personalizzato oppure selezionare il tipo di file di sistema. <br><br>Selezionare **Crea** per creare il tipo di file personalizzato.     |
    |**Selezionare le regole di classificazione**     |   Individuare e selezionare le regole di classificazione che si desidera eseguire nel set di dati.      |
    |     |         |

    Selezionare **Crea** al termine della creazione del set di regole.

1. Nel riquadro **imposta un trigger di analisi** selezionare una delle opzioni seguenti e quindi fare clic su **continua**:

    - **Ricorrenza** per configurare una pianificazione per un'analisi ricorrente
    - **Una volta** per configurare un'analisi che viene avviata immediatamente

1. Nel riquadro controlla **analisi** controllare i dettagli di analisi per verificare che siano corretti, quindi selezionare **Salva** o **Salva ed Esegui** se è stata selezionata **una volta** nel riquadro precedente.

    > [!NOTE]
    > Una volta avviata, l'analisi può richiedere fino a 24 ore per il completamento. Sarà possibile esaminare i **report di Insight** e cercare il catalogo 24 ore dopo l'avvio di ogni analisi.
    >

Per altre informazioni, vedere [esplorare i risultati dell'analisi di competenza](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Esplora i risultati dell'analisi di competenza

Una volta completata l'analisi di competenza nei bucket di Amazon S3, eseguire il drill-down nell'area **origini** di competenza per visualizzare la cronologia delle analisi.

Selezionare un'origine dati per visualizzarne i dettagli, quindi selezionare la scheda **scansioni** per visualizzare le analisi attualmente in esecuzione o completate.
Se è stato aggiunto un account AWS con più bucket, viene visualizzata la cronologia delle analisi per ogni bucket nell'account.

Ad esempio:

![Mostra le analisi del bucket di AWS S3 nell'origine dell'account AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Usare le altre aree di competenza per ottenere informazioni dettagliate sul contenuto nel proprio data, inclusi i bucket di Amazon S3:

- **Eseguire una ricerca nel Catalogo dati di competenza** e filtrare per un bucket specifico. Ad esempio:

    ![Eseguire una ricerca nel catalogo per le risorse di AWS s3.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Visualizzare i report di Insight** per visualizzare le statistiche per la classificazione, le etichette di riservatezza, i tipi di file e altri dettagli sul contenuto.

    Tutti i report di Insight Insight includono i risultati dell'analisi di Amazon S3, insieme al resto dei risultati delle origini dati di Azure. Quando pertinente, è stato aggiunto un altro tipo di asset **Amazon S3** alle opzioni di filtro dei report.

    Per ulteriori informazioni, vedere la pagina relativa [a Insights in Azure](concept-insights.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insight di Azure:

> [!div class="nextstepaction"]
> [Informazioni dettagliate in Azure Purview](concept-insights.md)
