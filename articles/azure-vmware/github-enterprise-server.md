---
title: Configurare GitHub Enterprise Server nel cloud soluzione Azure VMware privato
description: Informazioni su come configurare GitHub Enterprise Server nel cloud soluzione Azure VMware privato.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862940"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Configurare GitHub Enterprise Server nel cloud soluzione Azure VMware privato

In questo articolo vengono descritti i passaggi per configurare GitHub Enterprise Server, la versione "locale" di [GitHub.com](https://github.com/), nel cloud privato soluzione Azure VMware locale. Lo scenario che verrà descritto è un'istanza di GitHub Enterprise Server che può servire fino a 3.000 sviluppatori che eseguono fino a 25 processi al minuto GitHub Actions. Include la configurazione delle funzionalità di anteprima (al momento della *scrittura),* ad esempio GitHub Actions. Per personalizzare l'installazione in base alle esigenze specifiche, esaminare i requisiti elencati in Installazione di [GitHub Enterprise Server in VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Prima di iniziare

GitHub Enterprise Server richiede un codice di licenza valido. È possibile iscriversi per una licenza [di valutazione.](https://enterprise.github.com/trial) Se si sta cercando di estendere le funzionalità di GitHub Enterprise Server tramite un'integrazione, è possibile ottenere una licenza gratuita per sviluppatori a cinque postazioni. Richiedere questa licenza tramite [il programma Partner di GitHub.](https://partner.github.com/)

## <a name="installing-github-enterprise-server-on-vmware"></a>Installazione di GitHub Enterprise Server in VMware

Scaricare [la versione corrente di GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) per VMware ESXi/vSphere (OVA) e distribuire il modello [OVA](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) scaricato.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Distribuire il modello OVA.":::  

Specificare un nome riconoscibile per la nuova macchina virtuale, ad esempio GitHubEnterpriseServer. Non è necessario includere i dettagli sulla versione nel nome della macchina virtuale, perché questi dettagli diventano obsoleti quando l'istanza viene aggiornata. Selezionare tutte le impostazioni predefinite per il momento (questi dettagli verranno modificati a breve) e attendere l'importazione dell'OVA.

Dopo l'importazione, [modificare la configurazione hardware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) in base alle esigenze. In questo scenario di esempio sarà necessaria la configurazione seguente.

| Risorsa | Installazione standard | Configurazione standard + "Funzionalità beta" (azioni) |
| --- | --- | --- |
| vCPU | 4 | 8 |
| Memoria | 32 GB | 61 GB |
| Archivio collegato | 250 GB | 300 GB |
| Archiviazione radice | 200 GB | 200 GB |

Tuttavia, le esigenze possono variare. Vedere le linee guida relative alle considerazioni sull'hardware in [Installazione GitHub Enterprise Server in VMware.](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) Vedere anche [Aggiunta di risorse di CPU o memoria per VMware per](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) personalizzare la configurazione hardware in base alla situazione specifica.

## <a name="configuring-the-github-enterprise-server-instance"></a>Configurazione dell'istanza GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installare GitHub Enterprise.":::  

Dopo aver acceso la macchina virtuale (VM) di cui è stato appena effettuato il provisioning, [configurarla tramite il browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Verrà richiesto di caricare il file di licenza e impostare una password della console di gestione. Assicurarsi di annotarla in un luogo sicuro.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Accedere alla shell di amministrazione tramite SSH.":::    

È consigliabile eseguire almeno i passaggi seguenti:

1. Caricare una chiave SSH pubblica nella console di gestione, in modo che sia possibile [accedere alla shell amministrativa tramite SSH.](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) 

2. [Configurare TLS nell'istanza in](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) modo che sia possibile usare un certificato firmato da un'autorità di certificazione attendibile.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Configurazione dell'istanza.":::

Applicare le impostazioni.  Durante il riavvio dell'istanza, è possibile continuare con il passaggio successivo, **Configurazione dell'archiviazione BLOB per GitHub Actions**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Creare l'account amministratore.":::

Dopo il riavvio dell'istanza, è possibile creare un nuovo account amministratore nell'istanza. Assicurarsi di prendere nota anche della password dell'utente.

### <a name="other-configuration-steps"></a>Altri passaggi di configurazione

Per la protezione avanzata dell'istanza per l'uso in produzione, è consigliabile seguire questa procedura di configurazione facoltativa:

1. Configurare [la disponibilità elevata](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) per la protezione da:

    - Arresti anomali del software (sistema operativo o a livello di applicazione)
    - Errori hardware (archiviazione, CPU, RAM e così via)
    - Errori del sistema host di virtualizzazione
    - Rete logiche o fisicamente erre

2. [Configurare](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [le utilità di backup](https://github.com/github/backup-utils), fornendo snapshot con controllo delle versioni per il ripristino di emergenza, ospitati in disponibilità separata dall'istanza primaria.
3. [Configurare l'isolamento del](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)sottodominio, usando un certificato TLS valido, per mitigare lo scripting tra siti e altre vulnerabilità correlate.

## <a name="configuring-blob-storage-for-github-actions"></a>Configurazione dell'archiviazione BLOB per GitHub Actions

> [!NOTE]
> GitHub Actions è attualmente disponibile come versione beta limitata in [GitHub Enterprise Server versione 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

L'archiviazione BLOB esterna è necessaria per GitHub Actions in GitHub Enterprise Server (attualmente disponibile come funzionalità "beta"). Questa archiviazione BLOB esterna viene usata da Actions per archiviare artefatti e log. Actions on GitHub Enterprise Server [supporta Archiviazione BLOB di Azure come provider di archiviazione](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (e altri). Si eseguirà quindi il provisioning di un nuovo account di archiviazione di Azure con un [tipo di account di archiviazione](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BLOBStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Effettuare il Archiviazione BLOB di Azure account.":::

Al termine della distribuzione della nuova risorsa BlobStorage, copiare e prendere nota della stringa di connessione (disponibile in Chiavi di accesso). Questa stringa sarà necessaria a breve.

## <a name="setting-up-the-github-actions-runner"></a>Configurazione del runner GitHub Actions

> [!NOTE]
> GitHub Actions è attualmente disponibile come versione beta limitata in [GitHub Enterprise Server versione 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

A questo punto, dovrebbe essere in esecuzione un'istanza di GitHub Enterprise Server, con un account amministratore creato. È anche necessario disporre di archiviazione BLOB esterna che GitHub Actions verrà utilizzata per la persistenza.

Ora si creerà un punto in cui GitHub Actions da eseguire. anche in questo caso, si userà soluzione Azure VMware.

Prima di tutto, eseguire il provisioning di una nuova macchina virtuale nel cluster. La macchina virtuale verrà basata [su una versione recente di Ubuntu Server.](http://releases.ubuntu.com/20.04.1/)

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Effettuare il provisioning di una nuova macchina virtuale.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Effettuare il provisioning di una nuova macchina virtuale nel passaggio 2.":::

Dopo aver creato la macchina virtuale, accensione e connessione tramite SSH.

Installare quindi [l'applicazione Di esecuzione azioni,](https://github.com/actions/runner) che esegue un processo da un flusso GitHub Actions flusso di lavoro. Identificare e scaricare la versione x64 Linux più recente del runner Actions, dalla pagina [delle](https://github.com/actions/runner/releases) versioni o eseguendo lo script rapido seguente. Questo script richiede che curl [e jq](https://stedolan.github.io/jq/) siano presenti nella macchina virtuale.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

È ora necessario avere un file in locale nella macchina virtuale, actions-runner-linux-arm64- \* .tar.gz. Estrarre questo tarball in locale:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Questa estrazione decomprime alcuni file in locale, tra cui uno script e , a cui si tornerà `config.sh` `run.sh` a breve.

## <a name="enabling-github-actions"></a>Abilitazione GitHub Actions

> [!NOTE]
> GitHub Actions è attualmente disponibile come versione beta limitata in [GitHub Enterprise Server versione 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Quasi qui. Si configurerà e abiliterà GitHub Actions'istanza GitHub Enterprise Server. È necessario accedere alla shell amministrativa [dell'istanza](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)GitHub Enterprise Server tramite SSH e quindi eseguire i comandi seguenti:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Prossima esecuzione:

`ghe-actions-check -s blob`

Verrà visualizzato l'output: "L'archiviazione BLOB è integra".

Ora che GitHub Actions configurata, abilitarla per gli utenti. Accedere all'istanza di GitHub Enterprise Server come amministratore e selezionare l'icona ![ rocket.](media/github-enterprise-server/rocket-icon.png) nell'angolo superiore destro di qualsiasi pagina. Nella barra laterale sinistra selezionare **Enterprise overview (Panoramica aziendale),** **Quindi Policies**(Criteri), **Actions**(Azioni) e selezionare l'opzione per abilitare **Actions (Azioni) per tutte le organizzazioni.**

Configurare quindi il runner dalla **scheda Runner self-hosted** . Selezionare **Aggiungi nuovo** e quindi Nuovo **runner** dall'elenco a discesa.

Nella pagina successiva verrà visualizzato un set di comandi da eseguire. È sufficiente  copiare il comando per configurare il runner, ad esempio:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Copiare `config.sh` il comando e incollarlo in una sessione nello strumento di esecuzione azioni (creato in precedenza).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Runner azioni.":::

Usare il run.sh seguente per *eseguire il* runner:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Eseguire il runner.":::

Per rendere disponibile questo runner alle organizzazioni dell'organizzazione, modificare l'accesso dell'organizzazione:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Modificare l'accesso al runner.":::

In questo caso lo si rende disponibile per tutte le organizzazioni, ma è possibile limitare l'accesso a un subset di organizzazioni e anche a repository specifici.

## <a name="optional-configuring-github-connect"></a>(Facoltativo) Configurazione di GitHub Connect

Anche se questo passaggio è facoltativo, è consigliabile se si prevede di utilizzare azioni open source disponibili in GitHub.com. Consente di basarsi sul lavoro di altri utenti facendo riferimento a queste azioni riutilizzabili nei flussi di lavoro.

Per abilitare GitHub Connect, seguire la procedura descritta in Abilitazione dell'accesso automatico [GitHub.com azioni tramite GitHub Connect.](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)

Dopo aver abilitato GitHub Connect, selezionare l'opzione Server per usare le azioni **GitHub.com nelle esecuzioni del flusso di** lavoro.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Abilitare l'uso di azioni da GitHub.com nelle esecuzioni del flusso di lavoro.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Configurazione ed esecuzione del primo flusso di lavoro

Ora che Actions e GitHub Connect sono stati impostati, è possibile usare tutto questo lavoro. Ecco un flusso di lavoro di esempio che fa riferimento all'eccellente [octokit/request-action,](https://github.com/octokit/request-action)consentendo di "creare script" in GitHub tramite interazioni tramite l'API GitHub, basata su GitHub Actions.

In questo flusso di lavoro di base si userà per `octokit/request-action` aprire un problema in GitHub usando l'API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Flusso di lavoro di esempio.":::

>[!NOTE]
>GitHub.com ospita l'azione, ma quando viene eseguita in GitHub Enterprise  Server, usa automaticamente l'API GitHub Enterprise Server.

Se si sceglie di non abilitare GitHub Connect, è possibile usare il flusso di lavoro alternativo seguente.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Flusso di lavoro di esempio alternativo.":::

Passare a un repo nell'istanza e aggiungere il flusso di lavoro precedente come: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Un altro flusso di lavoro di esempio.":::

Nella scheda **Azioni** per il repo attendere l'esecuzione del flusso di lavoro.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Flusso di lavoro di esempio eseguito.":::

È anche possibile osservare l'elaborazione da parte del runner.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Flusso di lavoro elaborato dal runner.":::

Se tutto è stato eseguito correttamente, verrà visualizzato un nuovo problema nel proprio repo, intitolato "Hello world".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Esempio nel repo.":::

Congratulazioni! È stato appena completato il primo flusso di lavoro Actions in GitHub Enterprise Server, in esecuzione nel soluzione Azure VMware cloud privato.

In questo articolo viene impostata una nuova istanza di GitHub Enterprise Server, l'equivalente self-hosted di GitHub.com, sul cloud privato soluzione Azure VMware locale. Questa istanza include il supporto per GitHub Actions e usa Archiviazione BLOB di Azure per la persistenza di log ed artefatti. Ma stiamo semplicemente grafando la superficie di ciò che è possibile fare con GitHub Actions. Vedere l'elenco delle azioni nel [Marketplace di GitHub](https://github.com/marketplace)o [creare un file personalizzato.](https://docs.github.com/en/actions/creating-actions)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata trattata la configurazione di GitHub Enterprise Server nel soluzione Azure VMware cloud privato, è possibile ottenere informazioni su: 

- [Come iniziare a usare GitHub Actions](https://docs.github.com/en/actions)
- [Come partecipare al programma beta](https://resources.github.com/beta-signup/)
- [Amministrazione di GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)
