

_____________________________________________________________________________________________

http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/
------------------------------------------------------------------

var skype = require('botbuilder');

var express = require('express');

var azure = require('azure-storage');



var app = express();



var APP_ID = '';

var APP_SECRET = '';



var AZURE_ACCOUNT = '';

var AZURE_KEY = '';

var AZURE_TABLE = 'HolidaysHRBot';



var tableSvc = azure.createTableService(AZURE_ACCOUNT, AZURE_KEY);



var authenticated = false;

var holidays = false;

var sick = false;

var userId = '';

var userName = '';

var userEntity = undefined;



var botService = new skype.ChatConnector({

    appId: APP_ID,

    appPassword: APP_SECRET

});



var bot = new skype.UniversalBot(botService);



app.post('/api/messages', botService.listen());



userVerification = function(session) {

  session.send('Hey, let me verify your user id ' + userId + ' (' + userName + '), bear with me...');



  tableSvc.retrieveEntity(AZURE_TABLE, userId, userName, function entityQueried(error, entity) {

    if (!error) {

      authenticated = true;

      userEntity = entity;



      session.send('I have verified your id, how can I help you? Type a) for Holidays, b) for Sick Leave.');

    }

    else {

      session.send('Could not find: ' + userName + ', please make sure you use proper casing :)');

    }

  });

};



cleanUserId = function(userId) {

  var posi = userId.indexOf(':');

  return (posi > 0) ? userId.substring(posi + 1) : userId;

};



BotBrain = function(session) {

  var orig = session.message.text;

  var content = orig.toLowerCase();

  var from = session.message.user.name;

  

  if (authenticated) {

    if (content === 'a)') {

      holidays = true;

      session.send('Please indicate how many vacation days you will be requesting, i.e.: 3');

    }

    else if (content === 'b)') {

      sick = true;

      session.send('Please indicate how many sick days you will be requesting, i.e.: 2');

    }

    else if (content !== 'a)' && content !== 'b)') {

      if (holidays) {

        session.send(userName + '(' + userId + ')' + ', you have chosen to take ' + content + ' holiday(s). Session ended.');

        sick = false;

        authenticated = false;

      }

      else if (sick) {

        session.send(userName + '(' + userId + ')' + ', you have chosen to take ' + content + ' sick day(s). Session ended.');

        holidays = false;

        authenticated = false;

      }

      else if (!holidays && !sick) {

        session.send('I can only process vacation or sick leave requests. Please try again.');

      }

    }

  }

  else {

    authenticated = false, holidays = false, sick = false;

    userId = '', userName = '', userEntity = undefined;



    if (content === 'hi') {

      session.send('Hello ' + cleanUserId(from) + ', I shall verify your identify...');

      session.send('Can you please your provide your FirstName-LastName? (please use the - between them)');

    }

    else if (content !== '') {

      userId = cleanUserId(from);

      userName = orig;



      if (userName.indexOf('-') > 1) {

        userVerification(session);

      }

      else {

        session.send('Hi, please provide your FirstName-LastName (please use the - between them) or say hi :)');

      }

    }

  }

};



bot.dialog('/', function (session) {

  BotBrain(session);

});



app.get('/', function (req, res) {

  res.send('SkypeBot listening...');

});



//app.listen(3979, function () {

app.listen(process.env.port, function () {

  console.log('SkypeBot listening...');

});


________________________________________________________________

Installare l'interfaccia della riga di comando di Azure in Linux
................................................................
    Articolo
    13/09/2021
    27 minuti per la lettura

Scegliere un metodo di installazione

L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma che può essere installato localmente nei computer Linux. È possibile usare l'interfaccia della riga di comando di Azure in Linux per connettersi ad Azure ed eseguire comandi amministrativi sulle risorse di Azure. L'interfaccia della riga di comando in Linux consente l'esecuzione di vari comandi tramite un terminale usando prompt della riga di comando interattivi o uno script. Quando si è pronti per installare l'interfaccia della riga di comando di Azure in Linux, è consigliabile usare lo strumento di gestione pacchetti di una distribuzione Linux. Selezionare lo strumento di gestione pacchetti appropriato per la distribuzione dalle opzioni precedenti. Se non si dispone di uno dei gestori di pacchetti elencati, è possibile installare manualmente l'interfaccia della riga di comando di Azure in Linux selezionando l'opzione Installa script.

La versione corrente dell'interfaccia della riga di comando di Azure è 2.33.0. Per informazioni sulla versione più recente, vedere le note sulla versione. Per individuare la versione installata e verificare se è necessario aggiornarla, eseguire az version.
Panoramica

Lo strumento di gestione pacchetti apt contiene un pacchetto x86_64 per l'interfaccia della riga di comando di Azure che è stato testato nelle distribuzioni seguenti.
Panoramica
Distribuzione 	Versione
Ubuntu 	14.04 LTS (Trusty Tahir), 16.04 LTS (Xenial Xerus), 18.04 LTS (Bionic Beaver), 20.04 LTS (FocalIva), 21.04 (Hirsute Hippo)
Debian 	Debian 8 (Jessie), Debian 9 (Stretch), Debian 10 (Buster), Debian 11 (Bullseye)

Avviso

Ubuntu 20.04 (Focal Fossa) e 20.10 (Groovy Gorilla) includono un pacchetto azure-cli versione 2.0.81 fornito dal repository universe. Questo pacchetto è obsoleto e non è consigliato. Se il pacchetto è installato, rimuoverlo prima di continuare eseguendo il comando sudo apt remove azure-cli -y && sudo apt autoremove -y.

Il azure-cli pacchetto deb non supporta l'architettura ARM64.
Opzione di installazione

Sono disponibili due opzioni per installare l'interfaccia della riga di comando di Azure nel sistema. Prima di tutto, è possibile eseguire un singolo comando che scaricherà uno script di installazione ed eseguirà automaticamente i comandi di installazione. In alternativa, se si preferisce, è possibile eseguire manualmente i comandi di installazione con una procedura dettagliata. Entrambi i metodi sono descritti di seguito.
Opzione 1: Eseguire l'installazione con un unico comando

Il team che si occupa dell'interfaccia della riga di comando di Azure ha uno script per eseguire tutti i comandi di installazione in un unico passaggio. Questo script viene scaricato tramite curl e inviato tramite pipe direttamente a bash per installare l'interfaccia della riga di comando.

Per esaminare il contenuto dello script prima dell'esecuzione, è sufficiente scaricarlo usando curl ed esaminarlo nell'editor di testo preferito.
Bash

curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

Opzione 2: Istruzioni dettagliate per l'installazione

Se si preferisce una procedura di installazione dettagliata, completare i passaggi seguenti per installare l'interfaccia della riga di comando di Azure.

    Ottenere i pacchetti necessari per il processo di installazione:
    Bash

sudo apt-get update
sudo apt-get install ca-certificates curl apt-transport-https lsb-release gnupg

Scaricare e installare la chiave di firma Microsoft:
Bash

curl -sL https://packages.microsoft.com/keys/microsoft.asc |
    gpg --dearmor |
    sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null

Aggiungere il repository software dell'interfaccia della riga di comando di Azure:
Bash

AZ_REPO=$(lsb_release -cs)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" |
    sudo tee /etc/apt/sources.list.d/azure-cli.list

Aggiornare le informazioni del repository e installare il pacchetto azure-cli:
Bash

    sudo apt-get update
    sudo apt-get install azure-cli

Installare una versione specifica

È prima necessario configurare le azure-cli informazioni del repository come illustrato in precedenza. Le versioni disponibili sono disponibili nelle note sulla versione dell'interfaccia della riga di comando di Azure.

    Per visualizzare le versioni disponibili con il comando :
    Bash

apt-cache policy azure-cli

Per installare una versione specifica:
Bash

    sudo apt-get install azure-cli=<version>-1~bullseye

Risoluzione dei problemi

Di seguito sono riportati alcuni problemi comuni che possono verificarsi durante l'installazione con apt. Se si verifica un problema non trattato in questo articolo, segnalarlo in GitHub.
Nessun problema di moduli in Ubuntu 20.04 (Focal)/WSL

Se è stato installato azure-cli in Focal senza aggiungere il repository software dell'interfaccia della riga di comando di Azure nel passaggio 3 delle istruzioni di installazione manuale o tramite questo script, è possibile che si verifichino problemi perché il pacchetto azure-cli 2.0.81 installato dal repository focal/universe è obsoleto e quindi non vengono trovati moduli denominati 'decorator' o 'antlr4'. Rimuoverlo eseguendo sudo apt remove azure-cli -y && sudo apt autoremove -y, quindi seguire le istruzioni indicate in precedenza per installare il pacchetto azure-cli più recente.
lsb_release non restituisce la versione della distribuzione base corretta

Alcune distribuzioni derivate da Ubuntu o Debian, come Linux Mint, potrebbero non restituire il nome di versione corretto tramite lsb_release. Questo valore viene usato nel processo di installazione per determinare il pacchetto da installare. Se si conosce il nome in codice della versione di Ubuntu o Debian da cui deriva la distribuzione in uso, è possibile impostare manualmente il valore di AZ_REPO durante l'aggiunta del repository. In caso contrario, cercare le informazioni relative alla distribuzione in uso in merito a come determinare il nome in codice della distribuzione base e impostare AZ_REPO sul valore corretto.
Nessun pacchetto per la distribuzione in uso

Talvolta dopo il rilascio di una distribuzione può trascorrere tempo prima che sia disponibile un pacchetto dell'interfaccia della riga di comando di Azure per tale distribuzione. L'interfaccia della riga di comando di Azure è progettata per essere resiliente rispetto alle versioni future delle dipendenze e usarne il minor numero possibile. Se non è disponibile alcun pacchetto per la distribuzione base in uso, provare un pacchetto per una distribuzione precedente.

A tale scopo, impostare manualmente il valore di AZ_REPO durante l'aggiunta del repository. Usare il repository bionic per le distribuzioni Ubuntu e stretch per le distribuzioni Debian. Le distribuzioni rilasciate prima di Ubuntu Trusty e Debian Wheezy non sono supportate.
Installazione dell'interfaccia della riga di comando di Azure non riuscita in elementary OS (eOS)

eOS non riesce a installare l'interfaccia della riga di comando di Azure perché lsb_release restituisce HERA, ovvero il nome della versione di eOS. La soluzione consiste nel correggere il file /etc/apt/sources.list.d/azure-cli.list e modificare hera main in bionic main.

Contenuto del file originale:

deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ hera main

Contenuto del file modificato

deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ bionic main

Il proxy blocca la connessione

Se non è possibile connettersi a una risorsa esterna a causa di un proxy, assicurarsi di aver impostato correttamente le variabili HTTP_PROXY e HTTPS_PROXY nella shell. Per sapere quali host e porte usare per questi proxy, è necessario contattare l'amministratore di sistema.

Questi valori vengono rispettati da numerosi programmi Linux, inclusi quelli usati nel processo di installazione. Per impostare questi valori:
Bash

# No auth
export HTTP_PROXY=http://[proxy]:[port]
export HTTPS_PROXY=https://[proxy]:[port]

# Basic auth
export HTTP_PROXY=http://[username]:[password]@[proxy]:[port]
export HTTPS_PROXY=https://[username]:[password]@[proxy]:[port]

Importante

Se si è protetti da un proxy, è necessario impostare queste variabili della shell per connettersi ai servizi di Azure con l'interfaccia della riga di comando. Se non si usa l'autenticazione di base, è consigliabile esportare queste variabili nel file .bashrc. Seguire sempre i criteri di sicurezza aziendale e i requisiti definiti dall'amministratore di sistema.

Per usare sempre questo proxy, è opportuno configurare in modo esplicito apt. Assicurarsi che nella sezione /etc/apt/apt.conf.d/ di un file di configurazione di apt siano presenti le righe seguenti. È consigliabile usare il file di configurazione globale esistente, un file di configurazione proxy esistente, 40proxies o 99local. In ogni caso, attenersi ai requisiti previsti dall'amministrazione del sistema.
apt.conf

Acquire {
    http::proxy "http://[username]:[password]@[proxy]:[port]";
    https::proxy "https://[username]:[password]@[proxy]:[port]";
}

Se il proxy non usa l'autenticazione di base, rimuovere la parte [username]:[password]@ dell'URI del proxy. Per maggiori informazioni sulla configurazione del proxy, vedere la documentazione ufficiale di Ubuntu:

    Pagina del manuale su apt.conf
    Wiki di Ubuntu - Procedura per apt-get

Per ottenere la chiave di firma Microsoft e il pacchetto dal repository, il proxy deve consentire le connessioni HTTPS all'indirizzo seguente:

    https://packages.microsoft.com

Non è possibile installare o eseguire l'interfaccia della riga di comando nel sottosistema Windows per Linux

Dato che il sottosistema Windows per Linux (WSL) è un livello di conversione delle chiamate di sistema basato sulla piattaforma Windows, potrebbe verificarsi un errore quando si prova a installare o eseguire l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando usa alcune funzionalità che potrebbero contenere un bug in WSL. Se si verifica un errore indipendentemente dal modo in cui si installa l'interfaccia della riga di comando, è probabile che il problema riguardi WSL e non il processo di installazione dell'interfaccia della riga di comando.

Per risolvere i problemi dell'installazione di WSL e possibilmente i problemi riscontrati:

    Se possibile, eseguire un processo di installazione identico in un computer o una VM Linux per verificare se ha esito positivo. Se riesce, il problema è quasi certamente correlato a WSL. Per avviare una VM Linux in Azure, vedere la documentazione su come creare una VM Linux nel portale di Azure.
    Verificare di eseguire l'ultima versione di WSL. Per ottenere l'ultima versione, aggiornare l'installazione di Windows 10.
    Verificare la presenza di problemi aperti relativi a WSL che potrebbero risolvere il problema riscontrato. Spesso sono disponibili suggerimenti su soluzioni alternative al problema o informazioni su una versione in cui verrà risolto.
    Se non si trovano problemi esistenti per il problema riscontrato, segnalare un nuovo problema relativo a WSL assicurandosi di includere la maggiore quantità di informazioni possibile.

Se i problemi di installazione o esecuzione in WSL persistono, valutare la possibilità di installare l'interfaccia della riga di comando per Windows.
Aggiornamento

L'interfaccia della riga di comando include un comando predefinito per eseguire l'aggiornamento all'ultima versione:
Interfaccia della riga di comando di Azure

az upgrade

Nota

Il comando az upgrade è stato aggiunto nella versione 2.11.0 e non funziona con le versioni precedenti alla 2.11.0. Le versioni precedenti possono essere aggiornate reinstallando come descritto in Installare l'interfaccia della riga di comando di Azure.

Per impostazione predefinita, questo comando aggiorna anche tutte le estensioni installate. Per altre opzioni di az upgrade, vedere la pagina di informazioni di riferimento sui comandi.

È anche possibile usare apt-get upgrade per aggiornare il pacchetto dell'interfaccia della riga di comando.
Bash

sudo apt-get update && sudo apt-get upgrade

Nota

Questo comando aggiorna tutti i pacchetti installati nel sistema che non hanno subito modifiche relative alle dipendenze. Per eseguire l'aggiornamento solo dell'interfaccia della riga di comando, usare apt-get install.
Bash

sudo apt-get update && sudo apt-get install --only-upgrade -y azure-cli

Disinstallare

È possibile disinstallare l'interfaccia della riga di comando di Azure. Prima di eseguire la disinstallazione, usare il comando az feedback per segnalare aspetti da migliorare o correggere. L'obiettivo è realizzare un'interfaccia della riga di comando di Azure priva di bug e facile da usare. Se è stato rilevato un bug, segnalarlo in GitHub.

    Eseguire la disinstallazione con apt-get remove:
    Bash

sudo apt-get remove -y azure-cli

Se non si prevede di reinstallare l'interfaccia della riga di comando di Azure, rimuovere le relative informazioni del repository:
Bash

sudo rm /etc/apt/sources.list.d/azure-cli.list

Se non si usano altri pacchetti Microsoft, rimuovere la chiave di firma:
Bash

sudo rm /etc/apt/trusted.gpg.d/microsoft.gpg

Rimuovere eventuali pacchetti non necessari:
Bash

    sudo apt autoremove

Rimuovi dati

Se non si prevede di reinstallare l'interfaccia della riga di comando di Azure, rimuovere i dati.
Bash

rm -rf ~/.azure

Passaggi successivi

Ora che l'interfaccia della riga di comando di Azure è stata installata, passare a una breve presentazione delle relative funzionalità e dei comandi comuni.

Contenuto consigliato

    Come eseguire l'interfaccia della riga di comando di Azure in un contenitore Docker

    Informazioni su come eseguire un contenitore Docker che ospita l'interfaccia della riga di comando di Azure. Docker consente di iniziare rapidamente con un ambiente isolato in cui eseguire l'interfaccia della riga di comando di Azure.

_____________________________________________________________________________________________________________________________________________________________________________________________________

