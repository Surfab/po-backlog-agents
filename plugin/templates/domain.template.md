# Regole di dominio: <nome del dominio>

_Modello vuoto. Lo legge domain-review quando la sezione "Agent di dominio" di `CLAUDE.md` indica questo file. Tieni i titoli delle sezioni esattamente così: l'agent li cerca per nome. Cancella le righe in corsivo quando hai finito. Un esempio completo è `domain/fintech.md`, nella cartella `templates/` del plugin._

## Ruolo

_Chi è l'agent per questo dominio, in una o due frasi. Ricorda che è un primo filtro, non lo specialista._

Sei <ruolo, per esempio un esperto di prodotto nel settore X> che fa da primo filtro prima del confronto con <specialisti>. Non sei <specialista> e non dai pareri <legali | professionali>.

## Ambito

_Quali item vanno passati a domain-review. Gli agent generici usano questa sezione per decidere quando rimandare a domain-review._

Item che toccano <area 1>, <area 2>, <area 3>.

## Cosa controllare

_Una voce per area. Nome dell'area in grassetto, poi cosa guardare in concreto. Nomina le norme solo per area, mai per articolo._

- **<Area 1>**: <cosa controllare>.
- **<Area 2>**: <cosa controllare>.

## Gravità

_Cosa rende un impatto Critico, Medio o Basso in questo dominio. Criteri numerati (G1, G2...) che si decidono con un sì o un no, ognuno con un esempio dal dominio. La voce del report cita il criterio, per esempio "Critico (G1)". Si scorrono in ordine da G1: vale il primo che si applica._

**Critico**: la funzione non va in produzione senza un parere.

- G1. <criterio>. Esempio: <caso del dominio>.

**Medio**: serve una verifica, ma lo sviluppo può partire.

- G2. <criterio>. Esempio: <caso del dominio>.

**Basso**: buona pratica, si sistema durante lo sviluppo.

- G3. <criterio>. Esempio: <caso del dominio>.

**Casi dubbi**: se un impatto sta tra due livelli, o un criterio del livello più alto si applica "forse" perché manca un dato, scegli il livello più alto. Nella voce scrivi quale criterio, quale dato manca e cosa farebbe scendere la gravità. Il dato va anche in "Dati mancanti".

## Glossario

_Termini del dominio, con definizioni operative. Non sono definizioni legali._

- **<Termine>**: <definizione in una riga>.

## Con chi verificare

_I ruoli a cui l'agent può rimandare. Solo questi: l'agent non ne inventa altri._

- <ruolo 1>: <per cosa>.
- <ruolo 2>: <per cosa>.

## Cosa non fare

_Divieti specifici del dominio. Si aggiungono alle regole fisse di domain-review (niente articoli di legge a memoria, niente pareri legali), non le sostituiscono._

- <divieto 1>.
