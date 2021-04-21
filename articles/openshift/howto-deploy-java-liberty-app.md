---
title: Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4
description: Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783576"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4

Questa guida illustra come eseguire l'applicazione Java, Java EE, [Jakarta EE](https://jakarta.ee/)o [MicroProfile](https://microprofile.io/) nel runtime Open Liberty/WebSphere Liberty e quindi distribuire l'applicazione in contenitori in un cluster Azure Red Hat OpenShift (ARO) 4 usando l'operatore Open Liberty. Questo articolo illustra come preparare un'applicazione Liberty, compilare l'immagine Docker dell'applicazione ed eseguire l'applicazione in contenitori in un cluster ARO 4.  Per altri dettagli su Open Liberty, vedere [la pagina del progetto Open Liberty](https://openliberty.io/). Per altri dettagli su IBM WebSphere Liberty, vedere la pagina del prodotto [WebSphere Liberty.](https://www.ibm.com/cloud/websphere-liberty)

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare i prerequisiti seguenti per eseguire correttamente questa guida.

> [!NOTE]
> Azure Red Hat OpenShift richiede almeno 40 core per creare ed eseguire un cluster OpenShift. La quota di risorse di Azure predefinita per una nuova sottoscrizione di Azure non soddisfa questo requisito. Per richiedere un aumento del limite di risorse, vedere [Quota standard: aumentare i limiti per serie di macchine virtuali](../azure-portal/supportability/per-vm-quota-requests.md). Si noti che la sottoscrizione di valutazione gratuita non è idonea per un aumento della quota, eseguire l'aggiornamento [a](../cost-management-billing/manage/upgrade-azure-subscription.md) una sottoscrizione con pagamento in base al go prima di richiedere un aumento della quota.

1. Preparare un computer locale con sistema operativo simile a Unix installato (ad esempio, Ubuntu, macOS).
1. Installare un'implementazione java se (ad [esempio, AdoptOpenJDK OpenJDK 8 LTS/OpenJ9).](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)
1. Installare [Maven](https://maven.apache.org/download.cgi) 3.5.0 o versione successiva.
1. Installare [Docker](https://docs.docker.com/get-docker/) per il sistema operativo.
1. Installare [l'interfaccia della riga](/cli/azure/install-azure-cli) di comando di Azure 2.0.75 o versione successiva.
1. Controllare e installare [`envsubst`](https://command-not-found.com/envsubst) se non è preinstallato nel sistema operativo.
1. Clonare il codice per questo esempio nel sistema locale. L'esempio si trova in [GitHub.](https://github.com/Azure-Samples/open-liberty-on-aro)
1. Seguire le istruzioni in [Creare un cluster Azure Red Hat OpenShift 4](./tutorial-create-cluster.md).

   Anche se il passaggio "Ottenere un segreto pull Red Hat" è etichettato come facoltativo, **è necessario per questo articolo.**  Il segreto pull consente al cluster Azure Red Hat OpenShift di trovare Open Operatore Operatore Open Operator.

   Se si prevede di eseguire applicazioni a elevato utilizzo di memoria nel cluster, specificare le dimensioni appropriate della macchina virtuale per i nodi di lavoro usando il `--worker-vm-size` parametro . Ad esempio, `Standard_E4s_v3` è la dimensione minima della macchina virtuale per installare l'operatore Elasticsearch in un cluster. Per altre informazioni, vedere:

   * [Interfaccia della riga di comando di Azure per creare un cluster](/cli/azure/aro#az_aro_create)
   * [Dimensioni delle macchine virtuali supportate per l'ottimizzazione per la memoria](./support-policies-v4.md#memory-optimized)
   * [Prerequisiti per l'installazione dell'operatore Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Connettersi al cluster seguendo la procedura descritta in [Connettersi a un cluster Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Assicurarsi di seguire la procedura descritta in "Installare l'interfaccia della riga di comando di OpenShift" perché il comando verrà `oc` utilizzato più avanti in questo articolo.
   * Annota l'URL della console del cluster simile a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Prendere nota delle `kubeadmin` credenziali.

1. Verificare che sia possibile accedere all'interfaccia della riga di comando di OpenShift con il token per l'utente `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Abilitare il registro contenitori predefinito per OpenShift

I passaggi di questa esercitazione creano un'immagine Docker di cui è necessario eseguire il push in un registro contenitori accessibile a OpenShift. L'opzione più semplice è usare il Registro di sistema incorporato fornito da OpenShift. Per abilitare il registro contenitori predefinito, seguire la procedura descritta in Configurare il registro contenitori [predefinito per Azure Red Hat OpenShift 4.](built-in-container-registry.md) In questo articolo vengono usati tre elementi di questi passaggi.

* Nome utente e password dell Azure AD utente per l'accesso alla console Web OpenShift.
* Output di dopo `oc whoami` aver seguito i passaggi per l'accesso all'interfaccia della riga di comando di OpenShift.  Questo valore è denominato **aad-user per** la discussione.
* URL del registro contenitori.

Prendere nota di questi elementi quando si completano i passaggi per abilitare il registro contenitori predefinito.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Creare uno spazio dei nomi OpenShift per l'app Java

1. Accedere alla console Web OpenShift dal browser usando `kubeadmin` le credenziali.
2. Passare ad **Amministrazione Spazi** dei  >  **nomi Crea** spazio dei  >  **nomi**.
3. Compilare per `open-liberty-demo` **Nome e** selezionare **Crea**, come illustrato di seguito.

   ![creare lo spazio dei nomi](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Creare un amministratore per il progetto demo

Oltre alla gestione delle immagini, **all'utente aad** verranno concesse anche le autorizzazioni amministrative per la gestione delle risorse nel progetto demo del cluster ARO 4.  Accedere all'interfaccia della riga di comando di OpenShift e concedere **all'utente aad** i privilegi necessari seguendo questa procedura.

1. Accedere alla console Web OpenShift dal browser usando `kubeadmin` le credenziali.
1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente connesso e quindi **selezionare Copia comando di accesso**.
1. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
1. Selezionare **Visualizza token**.
1. Copiare il valore **elencato sotto Account di** accesso con questo token negli Appunti ed eseguirlo in una shell, come illustrato di seguito.
1. Eseguire i comandi seguenti per concedere `admin` il ruolo **all'utente aad nello** spazio dei nomi `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Installare l'operatore Open Liberty OpenShift

Dopo aver creato e connesso il cluster, installare l'operatore Open Liberty.  La pagina iniziale principale dell'operatore Open Liberty è disponibile in [GitHub.](https://github.com/OpenLiberty/open-liberty-operator)

1. Accedere alla console Web OpenShift dal browser usando `kubeadmin` le credenziali.
2. Passare a **Operatori**  >  **OperatorHub** e cercare **Operatore Open Liberty.**
3. Selezionare **Open Operator (Apri Operatore)** nei risultati della ricerca.
4. Selezionare **Installa**.
5. Nella finestra popup **Create Operator Subscription (Crea sottoscrizione operatore)** selezionare All **namespaces on the cluster (default) (Tutti** gli spazi dei nomi nel cluster - impostazione predefinita) per **Installation Mode**(Modalità di installazione), **beta** per **Update Channel**(Canale di aggiornamento) e **Automatic** for Approval Strategy (Strategia di approvazione **automatica):**

   ![Creare la sottoscrizione dell'operatore per Open Operatore Operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Selezionare **Subscribe** (Sottoscrivi) e attendere un minuto o due finché non viene visualizzato Open Operator (Operatore Open Operator).
7. Osservare l'operatore Open Operator (Operatore Open) con stato "Succeeded".  In caso contrario, diagnosticare e risolvere il problema prima di continuare.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Operatori installati che mostrano che Open Operators è installato.":::

## <a name="prepare-the-liberty-application"></a>Preparare l'applicazione Disartie

In questa guida si userà un'applicazione Java EE 8 come esempio. Open Passport è un server compatibile con profilo completo [Java EE 8,](https://javaee.github.io/javaee-spec/javadocs/) in modo da poter eseguire facilmente l'applicazione.  Open Passport è anche Compatibile con il profilo completo di [Jakarta EE 8.](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>Eseguire l'applicazione in Open Application

Per eseguire l'applicazione in Open Plugin, è necessario creare un file di configurazione del server Open Plugin in modo che il plug-in [Maven Possa](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) creare un pacchetto dell'applicazione per la distribuzione. Non è necessario il plug-in Maven per distribuire l'applicazione in OpenShift.  Verrà tuttavia utilizzato in questo esempio con la modalità sviluppatore (dev) di Open%.  La modalità sviluppatore consente di eseguire facilmente l'applicazione in locale. Completare i passaggi seguenti nel computer locale.

1. Copiare `2-simple/src/main/liberty/config/server.xml` `1-start/src/main/liberty/config` in , sovrascrivendo il file di lunghezza zero esistente. In `server.xml` questo modo viene configurato il server Open Modo con le funzionalità di Java EE.
1. Copiare `2-simple/pom.xml` in `1-start/pom.xml` .  Questo passaggio aggiunge `liberty-maven-plugin` l'oggetto al file POM.
1. Passare alla directory `1-start` del clone locale.
1. Eseguire `mvn clean package` in una console per generare un pacchetto war nella directory `javaee-cafe.war` `./target` .
1. Eseguire `mvn liberty:dev` per avviare Open Liberty in modalità di sviluppo.
1. Attendere l'avvio del server. L'output della console dovrebbe terminare con il messaggio seguente:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Aprire `http://localhost:9080/` nel browser per visitare l'applicazione home page. L'applicazione avrà un aspetto simile all'immagine seguente:

   ![Interfaccia utente Web javaee cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Premere **CTRL+C per** arrestare l'applicazione e aprire il server.

La directory `2-simple` del clone locale mostra il progetto Maven con le modifiche precedenti già applicate.

## <a name="prepare-the-application-image"></a>Preparare l'immagine dell'applicazione

Per distribuire ed eseguire l'applicazione Liberty in un cluster ARO 4, creare un contenitore dell'applicazione come immagine Docker usando immagini del contenitore [Open Liberty](https://github.com/OpenLiberty/ci.docker) o immagini del contenitore [WebSphere Liberty.](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Compilare un'immagine dell'applicazione

Completare la procedura seguente per compilare l'immagine dell'applicazione:

1. Passare alla directory `2-simple` del clone locale.
2. Eseguire `mvn clean package` per creare un pacchetto dell'applicazione.
3. Eseguire uno dei comandi seguenti per compilare l'immagine dell'applicazione.
   * Compilare con l'immagine di base Open Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Compilare con l'immagine di base di WebSphere Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Eseguire l'applicazione in locale con Docker

Prima di distribuire l'applicazione in contenitori in un cluster remoto, eseguire con il Docker locale per verificare se funziona:

1. Eseguire `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` nella console di .
2. Attendere l'avvio del server Disaparte e la corretta distribuzione dell'applicazione.
3. Aprire `http://localhost:9080/` nel browser per visitare l'applicazione home page.
4. Premere **CTRL+C per** arrestare l'applicazione e il server Disasse.

### <a name="push-the-image-to-the-container-image-registry"></a>Eseguire il push dell'immagine nel registro delle immagini del contenitore

Quando si è soddisfatti dello stato dell'applicazione, eseguire il push nel registro immagini contenitore predefinito seguendo le istruzioni riportate di seguito.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Accedere all'interfaccia della riga di comando di OpenShift come Azure AD utente

1. Accedere alla console Web OpenShift dal browser usando le credenziali di un Azure AD utente.

   1. Usare una funzionalità InPrivate, Incognito o un'altra finestra del browser equivalente per accedere alla console.
   1. Selezionare **openid**

   > [!NOTE]
   > Prendere nota del nome utente e della password utilizzati per accedere qui. Il nome utente e la password funzioneranno come amministratore per altre azioni in questo e in altri articoli.
1. Accedere con l'interfaccia della riga di comando di OpenShift seguendo questa procedura.  A scopo di discussione, questo processo è noto come `oc login` .
   1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente connesso e quindi selezionare **Copy Login Command (Copia comando di accesso).**
   1. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
   1. Selezionare **Display Token (Visualizza token).**
   1. Copiare il valore **elencato sotto Accedi con questo token** negli Appunti ed eseguirlo in una shell, come illustrato di seguito.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Eseguire il push dell'immagine del contenitore nel registro contenitori per OpenShift

Eseguire questi comandi per eseguire il push dell'immagine nel registro contenitori per OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

L'output corretto sarà simile al seguente.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Eseguire il push dell'immagine nel registro immagini contenitore predefinito con il comando seguente.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Distribuire l'applicazione nel cluster ARO 4

È ora possibile distribuire l'applicazione di esempio Liberty nel cluster Azure Red Hat OpenShift 4 creato in precedenza durante l'uso dei prerequisiti.

### <a name="deploy-the-application-from-the-web-console"></a>Distribuire l'applicazione dalla console Web

Poiché si usa l'operatore Open Liberty per gestire le applicazioni di Libertà, è necessario creare un'istanza della relativa definizione di risorsa personalizzata *di* tipo "OpenLibertyApplication". L'operatore si occuperà quindi di tutti gli aspetti della gestione delle risorse OpenShift necessarie per la distribuzione.

1. Accedere alla console Web OpenShift dal browser usando le credenziali dell'Azure AD utente.
1. Espandere **Home,** selezionare **Progetti**  >  **open-liberty-demo.**
1. Passare a **Operatori**  >  **installati Operatori**.
1. Al centro della pagina selezionare **Open Liberty Operator (Apri operatore Libertà).**
1. Al centro della pagina selezionare **Apri applicazione Libertà**.  La navigazione degli elementi nell'interfaccia utente rispecchia la gerarchia di contenimento effettiva delle tecnologie in uso.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java Containment](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selezionare **Crea OpenLibertyApplication**
1. Sostituire il file yaml generato con il proprio, che si trova in `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selezionare **Crea**. Verrà restituito l'elenco di OpenLibertyApplications.
1. Selezionare **javaee-cafe-simple.**
1. Al centro della pagina selezionare **Risorse.**
1. Nella tabella selezionare il collegamento per **javaee-simple con** **il tipo** di **route**.
1. Nella pagina visualizzata selezionare il collegamento sotto **Percorso**.

L'applicazione verrà aperta home page nel browser.

### <a name="delete-the-application-from-the-web-console"></a>Eliminare l'applicazione dalla console Web

Al termine dell'applicazione, seguire questa procedura per eliminare l'applicazione da Open Shift.

1. Nel riquadro di spostamento a sinistra espandere la voce **Operatori**.
1. Selezionare **Operatori installati**.
1. Selezionare **Open Operator ( Apri Operatore)**.
1. Al centro della pagina selezionare **Open The Application (Apri applicazione)**.
1. Selezionare i puntini di sospensione verticali (tre punti verticali) e quindi **selezionare Delete OpenLiberty Application (Elimina applicazione OpenLiberty).**

### <a name="deploy-the-application-from-cli"></a>Distribuire l'applicazione dall'interfaccia della riga di comando

Anziché usare l'interfaccia utente grafica della console Web, è possibile distribuire l'applicazione dall'interfaccia della riga di comando. Se non è già stato fatto, scaricare e installare lo strumento da riga di comando seguendo la documentazione di Red Hat Attività iniziali `oc` [con l'interfaccia della riga di comando](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Accedere alla console Web OpenShift dal browser usando le credenziali dell'Azure AD utente.
2. Accedere all'interfaccia della riga di comando di OpenShift con il token per l'Azure AD utente.
3. Passare alla directory del clone locale ed eseguire i comandi seguenti per distribuire `2-simple` l'applicazione Dellay nel cluster ARO 4.  Anche l'output del comando viene visualizzato inline.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Prima di `1/1` continuare, verificare `READY` che sia visualizzata sotto la colonna .  In caso contrario, analizzare e risolvere il problema prima di continuare.
5. Individuare l'host di route per l'applicazione `oc get route` con il comando , come illustrato di seguito.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Una volta che l'applicazione Liberty è in esecuzione, aprire l'output di **Route Host** nel browser per visitare l'applicazione home page.

### <a name="delete-the-application-from-cli"></a>Eliminare l'applicazione dall'interfaccia della riga di comando

Eliminare l'applicazione dall'interfaccia della riga di comando eseguendo questo comando.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il cluster ARO seguendo la procedura descritta in [Esercitazione: Eliminare un cluster Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come:
> [!div class="checklist"]
>
> * Preparare l'applicazione Liberty
> * Compilare l'immagine dell'applicazione
> * Eseguire l'applicazione in contenitori in un cluster ARO 4 usando l'interfaccia utente grafica e l'interfaccia della riga di comando

Per altre informazioni, vedere i riferimenti usati in questa guida:

* [Open Liberty](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Operatore Open Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Aprire la configurazione del server Disasserzione](https://openliberty.io/docs/ref/config/)
* [Plug-in Maven di Libertà](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Aprire le immagini del contenitore di Libertà](https://github.com/OpenLiberty/ci.docker)
* [Immagini del contenitore WebSphere Liberty](https://github.com/WASdev/ci.docker)