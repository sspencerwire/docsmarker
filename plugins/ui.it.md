---
title: User Interface
author: Franco Colussi
contributors: Steve Spencer
tags:
    - neovim
    - editor
    - markdown
---
<!--vale off-->
## Introduzione

Neovim include molti miglioramenti rispetto a Vim per quanto riguarda l'interfaccia utente e le prestazioni. Neovim presenta un'interfaccia utente moderna e personalizzabile, è stato progettato per essere più efficiente e veloce di Vim, con un'impronta di memoria ridotta e prestazioni migliori per i file di grandi dimensioni.

Nel complesso, l'interfaccia utente di Neovim è altamente personalizzabile e flessibile, consentendo agli utenti di creare un ambiente di editing personalizzato. Con la giusta combinazione di colori, statusline, font, mappature e plugin, è possibile creare un'esperienza di editing produttiva ed efficiente.

## Rocksmarker UI

In Rocksmarker per personalizzare l'interfaccia utente sono stati modificati i seguenti aspetti:

- **Colori**: Neovim supporta vari temi di colore che modificano il colore del testo, dello sfondo e di altri elementi dell'editor. Per la configurazione di *Rocksmarker* è stato utilizzato il tema bamboo.nvim.
- **Statusline**: La statusline visualizza informazioni quali il nome del file corrente, la posizione del cursore e la modalità. Neovim consente di personalizzare la statusline per visualizzare le informazioni desiderate nel formato preferito. Per questa funzionalità è stato scelto feline.nvim una statusline minimale ma altamente personalizzabile.
- **Bufferline**: La bufferline, assente nella installazione standard di Neovim, è una funzione molto diffusa per organizzare e navigare tra più buffer in Neovim. Una bufferline consente di vedere tutti i buffer aperti a colpo d'occhio e di passare facilmente da uno all'altro. Per questa funzione la configurazione di Rocksmarker utilizza il plugin nvim-cokeline, una bufferline che fornisce un framework generico su cui costruire la propria bufferline.
- **Caratteri**: Neovim consente di impostare il tipo e la dimensione dei caratteri dell'editor. È possibile scegliere tra una serie di font compatibili con il sistema operativo in uso.
- **Mappature dei tasti**: Neovim consente di mappare i tasti a comandi o funzioni personalizzate. Questa funzione consente di creare scorciatoie per i comandi utilizzati più di frequente o di eseguire operazioni complesse con la pressione di un solo tasto.

Tutte le personalizzazioni dell'aspetto sono impostate nelle sezioni relative nel file `lua/plugins/ui.lua`, e corrispondono al *bundle*:

```lua linenums="139"
[bundles.ui]
items = [
    "feline.nvim",
    "fidget.nvim",
    "nvim-cokeline",
    "gitsigns.nvim",
    "bamboo.nvim",
]
```

### bamboo.nvim

**Bamboo.nvim** fornisce un tema verde scuro per Neovim scritto in Lua con l'evidenziazione della sintassi di Tree-sitter e l'evidenziazione semantica di LSP.Il tema usa il blu e il viola con parsimonia per ridurre l'affaticamento degli occhi. Il rosso, il giallo e il verde sono invece più usati per lo stesso motivo.  
I commenti sono colorati in modo specifico per essere leggibili e avere un buon contrasto con il resto del testo e dello sfondo. Sono gestiti e colorati in modo appropriato molti token dell'evidenziazione semantica e sono inoltre supportati i plugin più comuni con i colori impostati in maniera appropriata.

```lua
require("bamboo").setup({
 -- Main options --
 style = "vulgaris",
 colors = {
    bright_orange = '#ff8800', -- Define a new color
    green = '#00ffaa', -- Redefine an existing color
 },
 highlights = {
  ["FloatBorder"] = { fg = "$grey" },
  ["IblIndent"] = { fg = "$bg1" },
  ["htmlH1"] = { fg = "$bg_blue" },
  ["htmlH2"] = { fg = "$green" },
  ....
  ....
 },
})
```

Il codice carica il plugin **bamboo** tramite la funzione *require('bamboo').setup*, imposta lo stile del tema e definisce due sezioni:

- **colors**: Questa sezione consente di definire nuovi colori o di ridefinire quelli esistenti. In questo caso, come esempio, viene definito un nuovo colore bright_orange e viene ridefinito il colore verde esistente.
- **highlights**: Questa sezione consente di definire l'evidenziazione della sintassi per vari costrutti linguistici e per le definizioni degli aspetti grafici di Neovim come bordi, sfondi e altro.

In questo modo si possono personalizzare i colori dell'interfaccia in modo flessibile ed estensibile.

!!! Tip "Ricavare il nome del token"

    Per ricavare i token semantici di *Neovim*, di *Tree-sitter* e dei vari *LSP* utilizzare il comando ==:Inspect==. La digitazione del comando, posizionando il cursore sulla parola o tag scelto, fornirà il nome del *token* (utilizzabile per la personalizzazione) e il suo colore attuale. Ricavato il nome del *token* è possibile ispezionare meglio le sue proprietà con il comando ==:Telescope highlights== inserendo nella ricerca il nome ottenuto con *Inspect*.

### feline.nvim

**Feline** è una statusline minimale e completamente personalizzabile, fornisce una struttura modulare su cui costruire facilmente la propria statusline, consente di modificare ogni minimo dettaglio a proprio piacimento. Si distingue per la facilità d'uso e per la completa personalizzazione di ogni componente.  
Fornisce i moduli per la visualizzazione di molti aspetti di Neovim come varie la modalità vi, le informazioni sui file, la dimensione dei file, la posizione del cursore, la diagnostica (usando l'LSP incorporato di Neovim), i rami e le differenze di git (usando gitsigns.nvim), ecc.

La configurazione del plugin inizia con la definizione del tema che sarà poi richiamato nella parte finale che si occupa di unire tutti i componenti:

```lua
local get_col = require("cokeline/utils").get_hex
local bamboo = {
 fg = get_col("Pmenu", "fg"),
 bg = get_col("TabLineFill", "bg"),
 bg3 = get_col("Normal", "bg"),
 grey = get_col("Comment", "fg"),
 green = get_col("String", "fg"),
 yellow = get_col("DiagnosticWarn", "fg"),
 purple = get_col("DiagnosticHint", "fg"),
 red = get_col("DiagnosticError", "fg"),
 cyan = get_col("DiagnosticInfo", "fg"),
 orange = get_col("WarningMsg", "fg"),
 coral = get_col("ErrorMsg", "fg"),
 blue = get_col("Function", "fg"),
}
```

I colori sono stati armonizzati con quelli forniti da bamboo.nvim per una migliore integrazione. Seguono poi le configurazioni dei moduli (*components*) da visualizzare  contenuti in tabelle annidate in una tabella denominata **c**, di seguito viene analizzato uno dei componenti della tabella la cui struttura è la seguente:

```lua
local c = {
 vim_mode = {

 },
....
....
 lsp_client_names = {

 },
...
...
}
```

Al suo interno troviamo, ad esempio, il modulo *vim_mode* che imposta i colori e l'aspetto dei vari stati  di Vim (INSERT, VISUAL, ...):

```lua linenums="108"
 vim_mode = {
  provider = {
   name = "vi_mode",
   opts = {
    show_mode_name = true,
    padding = "center",
   },
  },
  hl = function()
   return {
    name = require("feline.providers.vi_mode").get_mode_highlight_name(),
    bg = require("feline.providers.vi_mode").get_mode_color(),
    fg = "bg3",
    style = "bold",
   }
  end,
  left_sep = "block",
  right_sep = "right_rounded",
 },
```

La tabella **vim_mode** definisce i seguenti parametri del modulo. La tabella **provider** specifica il provider di questo componente, responsabile della generazione del contenuto da visualizzare. In questo caso, il provider è *vi_mode*, che visualizza la modalità Vim corrente.  
La tabella **opts** all'interno della tabella provider imposta le opzioni per il provider, come ad esempio se mostrare il nome della modalità e come allineare il nome della modalità all'interno del componente.  
La funzione **hl** definisce l'evidenziazione del componente. Utilizza la funzione *require(“feline.providers.vi_mode”)* per ottenere il nome e *require("feline.providers.vi_mode").get_mode_color()* per il colore dell'evidenziazione per la modalità Vim corrente, quindi imposta il colore di sfondo, il colore di primo piano e lo stile del testo.  
I campi **left_sep** e **right_sep** definiscono lo stile per i separatori da usare rispettivamente sui lati sinistro e destro del componente.  
Segue la definizione vera e propria della statusline:

```lua
local left = {
 c.vim_mode,
 c.gitBranch,
    ....
}

local middle = {
 c.lsp_client_names,
 c.diagnostic_errors,
    ....
}

local right = {
 -- c.file_type,
 c.file_encoding,
    ....
}

local components = {
 active = {
  left,
  ....
 },
 inactive = {
  left,
  ....
 },
}

```

Il codice definisce tre tabelle: **left**, **middle** e **right**, che rappresentano rispettivamente i componenti allineati a sinistra, al centro e a destra.  
La tabella di sinistra contiene i componenti che devono essere visualizzati sul lato sinistro della bufferline, come la modalità Vim corrente (*c.vim_mode*) e il ramo Git corrente (*c.gitBranch*).  
La tabella centrale contiene i componenti che devono essere visualizzati al centro come i nomi dei client LSP correnti (*c.lsp_client_names*) e gli errori diagnostici (*c.diagnostic_errors*).  
La tabella di destra contiene i componenti come la codifica corrente del file (*c.file_encoding*).

La tabella dei **components** è la tabella principale che definisce la struttura complessiva della bufferline. Ha due chiavi: *active* e *inactive*. La chiave attiva contiene un elenco dei componenti che devono essere visualizzati quando la riga di stato è attiva, mentre la chiave inattiva contiene un elenco dei componenti che devono essere visualizzati quando la riga di stato è inattiva. Questa funzione in questa configurazione non viene utilizzata.

Questo codice fornisce un modo flessibile e modulare per definire i componenti della bufferline, è possibile aggiungere, rimuovere o riorganizzare facilmente i componenti modificando le tabelle corrispondenti.

Al termine abbiamo la funzione che unisce tutte le impostazioni precedenti:

```lua linenums="290"
feline.setup({
 components = components,
 theme = bamboo,
 vi_mode_colors = vi_mode_colors,
})
```

Il codice configura attraverso la funzione *feline.setup()* il parametro **components**, la tabella cioè che definisce i diversi componenti da visualizzare nella statusline e successivamente il parametro **theme** che definisce i colori e gli stili da utilizzare, per ultimo viene configurato il parametro **vi_mode_colors** che definisce i colori da usare per le diverse modalità di Vi (ad esempio, modalità normale, modalità inserimento, modalità visuale, ecc.). Ciò consente alla statusline di cambiare aspetto in base alla modalità Vi, rendendo più facile l'identificazione della modalità corrente.

### nvim-cokeline

```lua linenums="298"
local get_hex = require("cokeline/utils").get_hex

local green = vim.g.terminal_color_2
local yellow = vim.g.terminal_color_3

require("cokeline").setup({
 default_hl = {
  fg = function(buffer)
   return buffer.is_focused and get_hex("Normal", "fg") or get_hex("Conceal", "fg")
  end,
  bg = get_hex("FloatermBorder", "bg"),
 },
```

Il codice inizia richiedendo la funzione **get_hex** del modulo **cokeline/utils**, che viene utilizzata per ottenere i valori esadecimali dei colori per le varie evidenziazioni di Neovim, quindi imposta le variabili verde e giallo ai valori delle variabili globali di Neovim *terminal_color_2* e *terminal_color_3*, rispettivamente.  
La parte principale del codice è la funzione *cokeline.setup()*, che configura la tabella **default_hl** impostando i gruppi di evidenziazione predefiniti per i buffer. La funzione **fg** restituisce il colore di primo piano in base al fatto che il buffer sia focalizzato o meno, il colore viene ricavato con la funzione *get_hex()*. La funzione **bg** è impostata sul colore di sfondo del gruppo di evidenziazione *FloatermBorder*.

La tabella dei **components**, anche in questo caso è modulare e contiene le parti da visualizzare. Il codice seguente è un estratto della tabella a scopo dimostrativo:

```lua linenums="311"
 components = {
  {
   text = "｜",
   fg = function(buffer)
    return buffer.is_modified and yellow or green
   end,
  },
  ....
  ....
```

Il codice imposta nella tabella dei componenti un singolo componente con il testo “｜” utilizzando poi la funzione fg per impostare il colore di primo piano del componente. Se il buffer è modificato, utilizza il colore giallo, altrimenti il colore verde.

Questo codice imposta l'aspetto del plugin Cokeline, compresi i colori di primo piano e di sfondo dei buffer e l'indicatore del buffer modificato.

Questo codice configura il plugin Cokeline con gruppi di evidenziazione e componenti di buffer personalizzati, fornendo una visualizzazione del buffer visivamente accattivante e informativa in Neovim.
