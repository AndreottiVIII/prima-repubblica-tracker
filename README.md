# Prima Repubblica Tracker

Chi è ancora vivo, fra i parlamentari e i ministri della Prima Repubblica.
Dalla Costituente del 1946 all'XI legislatura, più i governi Ciampi e Dini.

**https://andreottiviii.github.io/prima-repubblica-tracker/**

Al 29 agosto 2026: 4.603 schede, 796 viventi, 3.804 deceduti, 3 di sorte ignota.

## Come funziona

Questo repository non contiene il motore: contiene solo la pagina pubblicata e
il lavoro notturno che va a prenderla.

I dati, gli script che li scaricano e tutta la documentazione stanno in
**[duri-a-morire](https://github.com/AndreottiVIII/duri-a-morire)**, che è lo
stesso progetto sotto un altro nome. Là ogni notte l'elenco viene ricostruito
incrociando quattro fonti — Wikidata, gli open data della Camera dei deputati e
quelli del Senato, le voci di Wikipedia — e da un unico modello escono due edizioni identiche salvo
il nome.

Qui si scarica quella col nome sobrio e la si pubblica, due volte al giorno. Il
vantaggio è che i due siti non possono divergere, e che le fonti vengono
interrogate una volta sola per entrambi.

Se il file scaricato è troppo piccolo, o non è l'edizione giusta, il lavoro si
ferma e resta pubblicata quella del giorno prima: meglio un sito fermo a ieri
che un sito peggiore di ieri.

## Se qualcosa non va

Il posto da guardare è quasi sempre la sorgente. Se là il sito è aggiornato e
qui no, si rilancia a mano da `Actions` → `Rispecchia e pubblica` →
`Run workflow`.
