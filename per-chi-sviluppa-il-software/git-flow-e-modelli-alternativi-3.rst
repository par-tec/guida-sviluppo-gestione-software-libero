.. _git-flow-e-modelli-alternativi-3:

Git Flow e modelli alternativi
=================================================

Questa sezione mostra come utilizzare il git flow per contribuire ad un progetto open source,
ed è destinata ad una platea di sviluppatori che hanno familiarità con git.

Le considerazioni sono valide non solo per i progetti open source,
ma per tutti i progetti che hanno una durata di vita lunga e/o
che sono sviluppati da più persone.

Fare una PR
-----------

Per mantenere il codice pulito e coerente nel tempo,
facilitare la manutezione e ridurre il deployment di versioni inutili
(e.g. quando si utilizza una CI/CD pipeline),
i contributi a un repository devono passare da una Pull Request (PR).

Un processo di sviluppo che include delle PR corredate da **Code Review**,
è parte integrante di una strategia di prevenzione (c.d. **Shift-Left**) delle anomalie.

La procedura di seguito permette di creare una PR chiara,
in cui le PR sono associate ad un branch e ad una issue.

1. se non ci sono issue associati alla PR, creane uno che descriva
   il comportamento atteso e il comportamento attuale;

2. se non sei un membro dell'organizzazione e/o del team di sviluppo,
   fai un fork del repository e scarica (fetch) sia il tuo fork che il repository
   originale, che chiameremo `upstream` ::

        GH=ioggstream # usa il tuo nome utente github
        git clone -o par-tec https://github.com/par-tec/python-cookiecutter
        cd python-cookiecutter
        git remote add origin git@github.com:${GH}/python-cookiecotter.git

3. crea una branch per la tua PR facendo il fetch dal branch principale,
   usando il tuo nome utente e il numero di issue come nome del branch.
   Prima del checkout, assicurati di avere l'ultima versione del ramo `par-tec/main`,
   inoltre evita di fare il checkout del `main` locale e parti sempre dall'`upstream`  ::

        ISSUE=123 # Utilizzare il numero dell'issue.
        git fetch --all
        git checkout -b ${GH}-${ISSUE} par-tec/main

   In alcuni casi, i progetti possono adottare delle convenzioni per i nomi dei branch
   a seconda che l'issue sia una feature, una manutenzione ordinaria (c.d. `chore`) o una release.

4. apporta le modifiche (questo include l'esecuzione dei controlli di pre-commit) e rivedile durante l'aggiunta.
   Questo è un passo importante ma spesso trascurato, specialmente quando
   stai lavorando da solo o su una PR larga (che modifica molti file).
   Inoltre questo ti consente di suddividere le modifiche in più commit
   o di scartare alcune delle modifiche che vuoi ancora mantenere nella tua directory di lavoro.

        git add -p

5. ora puoi effettuare il commit. Se la tua PR risolve definitivamente l'issue,
   il messaggio di commit dovrebbe iniziare con `Fix: #ISSUE` dove  `ISSUE` è il numero di issue.
   In questo caso, quasi tutte le piattaforme di code-hosting aggiornano e/o chiudono automaticamente l'issue.
   Altrimenti, un riferimento all'issue può essere aggiunto nel corpo del messaggio di commit. ::

        git add .
        git commit -m "Fix: #123"

6. finito il lavoro, puoi pubblicare il tuo branch sul tuo fork e creare la PR.
   Se il tuo branch è stato pubblicato sul tuo fork, puoi creare la PR
   direttamente da github o gitlab. ::

        git push origin ${GH}-${ISSUE}

   da qui puoi aprire la PR tramite l'interfaccia web della piattaforma di code-hosting,
   indicando:

   - se la tua PR è ancora in lavorazione (draft PR), prefissala con l'etichetta
     `WIP:` (Work in Progress) e/o utilizza le funzionalità di **draft PR** della piattaforma
     di code-hosting;
   - il branch di destinazione (e.g. `par-tec/main`);
   - cosa è stato fatto, inclusi gli issue corretti (e.g. `Fix: #123`);
   - quando utile, una descrizione della soluzione.

7. una volta che la tua PR è stata integrata (merged) nel branch principale,
   puoi eliminare il branch locale e quello remoto.
   Il code-hosting permette di eliminare il branch remoto automaticamente,
   subito dopo il merge, tramite una configurazione effettuata tramite l'interfaccia web.


Modelli alternativi
-------------------

Alternativamente al git flow sono stati proposti altri modelli di contribuzione,
quali il trunk-based development.

Il trunk-based development è un approccio alla gestione del repository dove ::

   developers collaborate on code in a single branch called `trunk`,
   resist any pressure to create other long-lived development branches

   da https://trunkbaseddevelopment.com/

questo con l'obiettivo di ridurre la complessità della fase di integrazione
(merge) tra i vari branch, ed evitare la proliferazione di branch che spesso
vengono dimenticati e non eliminati.

Questo modello quindi è solo apparentemente più semplice da gestire,
poiché il ridotto uso dei branch e delle PR aumenta la probabilità
di integrare modifiche non sufficientemente validate.


pre-commit
----------

.. _pre-commit-3:

Pre-commit è un software python che controlla i tuoi file prima del commit.
Può fare lint, formattare o fare ulteriori controlli.

Una volta installato tramite ::

        pip3 install pre-commit --user

puoi eseguirlo direttamente tramite ::

        pre-commit run --all-files

O installalo come un pre-commit hook per eseguirlo
automaticamente prima di ogni commit ::

        pre-commit --install


Continuous Integration
----------------------

Ogni PR dev'essere testata da un flusso di lavoro in CI.
Nella maggior parte dei casi, è conveniente che questo flusso sia gestito tramite le funzionalità
esposte dalla piattaforma di code hosting (e.g. github actions, gitlab ci/cd, etc.).
Il passaggio finale potrebbe includere la pubblicazione (distribuzione) su un package registry,
quale pypi (python package index), maven central, o in OCI image registry.
