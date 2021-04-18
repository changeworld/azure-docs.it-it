---
title: Gestire utenti e ruoli in Azure IoT Central'applicazione | Microsoft Docs
description: Come gestire utenti e ruoli nell'applicazione Azure IoT Central amministratore
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 928a9fbad7bde7fe7f1cfaf181bff2a7b9ce458b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599061"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gestire utenti e ruoli nell'applicazione IoT Central

Questo articolo descrive come un amministratore può aggiungere, modificare ed eliminare utenti nell'applicazione Azure IoT Central utente. L'articolo descrive anche come gestire i ruoli nell'applicazione.

## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione. IoT Central attualmente supporta account Microsoft e Azure Active Directory, ma non Azure Active Directory gruppi.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.
    
    > [!div class="mx-imgBorder"]
    >![Gestire gli utenti](media/howto-manage-users-roles/manage-users-pnp.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    > [!div class="mx-imgBorder"]
    >![Aggiungere un utente e selezionare un ruolo](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Un utente che ha un ruolo personalizzato che concede l'autorizzazione per aggiungere altri utenti può aggiungere utenti solo a un ruolo con le stesse autorizzazioni o un numero inferiore rispetto al proprio ruolo.
    > 
    > Se un utente viene eliminato Azure Active Directory e quindi aggiunto di nuovo, non sarà in grado di accedere automaticamente all'IoT Central applicazione. Per abilitare nuovamente l'accesso, l'amministratore dell'applicazione deve eliminare e aggiungere nuovamente l'utente anche nell'applicazione.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo l'assegnazione. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> I ruoli assegnati sono specifici dell'applicazione IoT Central e non possono essere gestiti dal portale di Azure.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestisci ruoli

I ruoli consentono di controllare chi all'interno dell'organizzazione è autorizzato a eseguire varie attività in IoT Central. Sono disponibili tre ruoli predefiniti che è possibile assegnare agli utenti dell'applicazione. È anche possibile [creare ruoli personalizzati](#create-a-custom-role) se è necessario un controllo con granularità più fine.

> [!div class="mx-imgBorder"]
> ![Gestire la selezione dei ruoli](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Amministratore

Gli utenti con **il ruolo Amministratore** possono gestire e controllare ogni parte dell'applicazione, inclusa la fatturazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="builder"></a>Generatore

Gli utenti con **il ruolo Generatore** possono gestire ogni parte dell'app, ma non possono apportare modifiche nelle schede Amministrazione o Esportazione continua dei dati.

### <a name="operator"></a>Operatore

Gli utenti con il **ruolo Operatore** possono monitorare l'integrità e lo stato del dispositivo. Non è consentito apportare modifiche ai modelli di dispositivo o amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire i set di dispositivi ed eseguire analisi e processi. 

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Se la soluzione richiede controlli di accesso con granularità più fine, è possibile creare ruoli con set personalizzati di autorizzazioni. Per creare un ruolo personalizzato, passare alla **pagina Ruoli** **nella** sezione Amministrazione dell'applicazione. Selezionare quindi **+ Nuovo ruolo** e aggiungere un nome e una descrizione per il ruolo. Selezionare le autorizzazioni richieste dal ruolo e quindi selezionare **Salva**.

È possibile aggiungere utenti al ruolo personalizzato nello stesso modo in cui si aggiungono utenti a un ruolo predefinito.

> [!div class="mx-imgBorder"]
> ![Creare un ruolo personalizzato](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opzioni del ruolo personalizzato

Quando si definisce un ruolo personalizzato, si sceglie il set di autorizzazioni concesse a un utente se è membro del ruolo. Alcune autorizzazioni dipendono da altre. Ad esempio, se si aggiunge **l'autorizzazione Aggiorna dashboard applicazione** a un ruolo, è necessaria anche l'autorizzazione Visualizza dashboard **applicazione.** Le tabelle seguenti riepilogano le autorizzazioni disponibili e le relative dipendenze che è possibile usare per la creazione di ruoli personalizzati.

#### <a name="managing-devices"></a>Gestione dei dispositivi

**Autorizzazioni per i modelli di dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Gestire | Visualizzazione <br/> Altre dipendenze: visualizzare le istanze del dispositivo  |
| Controllo completo | Visualizzare, gestire <br/> Altre dipendenze: visualizzare le istanze del dispositivo |

**Autorizzazioni per l'istanza del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi |
| Update | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Create | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Delete | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Eseguire i comandi | Update, View <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Visualizzare dati non elaborati | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare, eseguire comandi, visualizzare i dati non elaborati <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi  |

**Autorizzazioni per i gruppi di dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e le istanze di dispositivo |
| Update | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e le istanze di dispositivo   |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e le istanze di dispositivo   |
| Delete | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e le istanze di dispositivo  |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e le istanze di dispositivo |

**Autorizzazioni di gestione della connettività dei dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Leggere un'istanza | nessuno <br/> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze di dispositivo |
| Gestire l'istanza | Leggere un'istanza <br /> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze del dispositivo |
| Lettura globale | nessuno   |
| Gestire le impostazioni globali | Lettura globale |
| Controllo completo | Istanza di lettura, Gestisci istanza, Lettura globale, Gestisci globale <br/> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze del dispositivo |

**Autorizzazioni per i processi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivo e gruppi di dispositivi |
| Update | Visualizzazione <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivo e gruppi di dispositivi |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivo e gruppi di dispositivi |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Execute | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi nelle istanze del dispositivo |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione, esecuzione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi nelle istanze del dispositivo |

**Autorizzazioni per le regole**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Update | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Delete | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: Visualizzare i modelli di dispositivo |

#### <a name="managing-the-app"></a>Gestione dell'app

**Autorizzazioni delle impostazioni dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Copia | Visualizzazione <br/> Altre dipendenze: Visualizzare modelli di dispositivo, istanze di dispositivo, gruppi di dispositivi, dashboard, esportazione dei dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, copiare, eliminare <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |

**Autorizzazioni di esportazione del modello di applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Esportazione | Visualizzazione <br/> Altre dipendenze: Visualizzare modelli di dispositivo, istanze di dispositivo, gruppi di dispositivi, dashboard, esportazione dei dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |
| Controllo completo | Visualizzare, esportare <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |

**Autorizzazioni per il caricamento di file del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Gestire | Visualizzazione   |
| Controllo completo | Visualizzare, gestire |

**Autorizzazioni di fatturazione**

| Nome | Dependencies |
| ---- | -------- |
| Gestire | nessuno     |
| Controllo completo | Gestire |

#### <a name="managing-users-and-roles"></a>Gestione di utenti e ruoli

**Autorizzazioni per ruoli personalizzati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno |
| Update | Visualizzazione |
| Create | Visualizzare, aggiornare |
| Delete | Visualizzazione |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni di gestione dell'utente**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Add | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Controllo completo | Visualizzazione, aggiunta, eliminazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |

> [!NOTE]
> Un utente che ha un ruolo personalizzato che concede loro l'autorizzazione per aggiungere altri utenti può aggiungere utenti solo a un ruolo con le stesse autorizzazioni o un numero inferiore rispetto al proprio ruolo.

#### <a name="customizing-the-app"></a>Personalizzazione dell'app

**Autorizzazioni del dashboard dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione |

**Autorizzazioni per i dashboard personali**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare   |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione |

**Autorizzazioni per personalizzazione, favicon e colori**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare |

**Autorizzazioni per i collegamenti della Guida**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare |

#### <a name="extending-the-app"></a>Estensione dell'app

**Autorizzazioni per l'esportazione dei dati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare  |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione |

**Autorizzazioni del token API**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno  <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Create | Visualizzazione <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Delete | Visualizzazione <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Controllo completo | Visualizzare, creare, eliminare <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire utenti e ruoli nell'applicazione IoT Central, il passaggio successivo consigliato consiste nell'apprendere come gestire [la fattura.](howto-view-bill.md)