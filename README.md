# VYON IPTV — player IPTV personale

App Android (Kotlin + Jetpack Compose) per leggere una playlist M3U / Xtream Codes
(`get.php?username=...&password=...&type=m3u_plus&output=ts`) e navigarla in tre
sezioni: **Diretta TV**, **Film**, **Serie TV**.

## Come aprirlo
1. Apri la cartella `MyDramaIPTV` con Android Studio (Hedgehog/Iguana o più recente).
2. Lascia che Android Studio scarichi il Gradle wrapper al primo sync (serve internet
   solo per quello, poi builda in locale).
3. **Manca l'icona dell'app**: Android Studio → tasto destro su `res` →
   *New → Image Asset* per generare `ic_launcher` con il logo rosa-oro di MyDrama
   (l'ho lasciato fuori perché non posso generare file immagine binari qui).
4. Avvia su device/emulatore.

## Come funziona
- **Al primo avvio** ti chiede i dati di accesso in stile Xtream Smarters/Chris TV:
  **Nome lista** (etichetta, facoltativa) + **Username** + **Password** + **Host/Server**.
  Da questi VYON costruisce internamente l'URL `get.php?...&type=m3u_plus&output=ts`.
  In alternativa, dal link "Ho già un URL M3U completo" puoi incollare direttamente
  un URL M3U pronto (utile per liste non-Xtream). La scelta viene salvata in locale
  (DataStore) e riusata ai lanci successivi.
- `M3uParser.kt` legge `#EXTINF` + attributi (`tvg-id`, `tvg-logo`, `group-title`)
  e smista ogni voce in Diretta TV / Film / Serie TV in base a euristiche su
  `group-title` e sul nome (es. rileva `S01E03` → Serie TV, raggruppa gli episodi
  per nome serie).
- Il player (`PlayerActivity.kt`) usa **Media3/ExoPlayer**, che gestisce sia stream
  live (HLS/.ts) sia file VOD (mp4/mkv) con lo stesso codice — non serve un player
  diverso per Live TV e Film/Serie.
- Tema colori in `ui/theme/Theme.kt`: rosa/magenta (`#E91E63`) su sfondo scuro,
  nessun oro come richiesto.

## Da rifinire quando avrai la lista vera
- Il parsing dei gruppi (LIVE_TV / MOVIE / SERIES) si basa su euristiche
  (`detectType` in `M3uParser.kt`). Appena hai un estratto reale della lista del
  fansub della tua amica, si può tarare la regex/le parole chiave esatte usate nei
  `group-title` per una categorizzazione perfetta al 100% invece che euristica.
- Al momento non c'è gestione EPG (guida programmi), preferiti, o ricerca — sono
  passi successivi naturali una volta validato che parser + player funzionano bene
  con la struttura reale della vostra lista.
- Nessuna icona app inclusa (vedi sopra).

## Test consigliato
Per un primo test end-to-end senza usare credenziali di terzi, prova con una lista
pubblica legale con la stessa sintassi M3U/EXTINF, ad es. quelle di iptv-org
(https://github.com/iptv-org/iptv) — stessa struttura, contenuti gratuiti e legali,
utile solo per verificare che tutta la pipeline (parser → categorie → player)
funzioni prima di collegare la lista vera del fansub.
