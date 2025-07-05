# Maglia

[](https://travis-ci.org/kiwi0fruit/knitty)![Stato di compilazione](https://travis-ci.org/kiwi0fruit/knitty.svg?branch=master)

Knitty è un filtro Pandoc e uno strumento di generazione di report riproducibili e compatibili con [Atom/Hydrogen](https://atom.io/packages/hydrogen) tramite Jupyter, Pandoc e Markdown (fork di [Stitch](https://github.com/kiwi0fruit/knitty/blob/master/docs/stitch.md) , una libreria simile a [Knitr](http://yihui.name/knitr/) - [RMarkdown](http://rmarkdown.rstudio.com) in Python). È possibile inserire codice Python (o altro codice kernel Jupyter) nel documento Markdown oppure **scrivere in Python/Julia/R/qualsiasi linguaggio kernel** con Markdown commentato a blocchi e visualizzare i risultati del codice nel documento di output Pandoc.

Vedi [**la documentazione di Knitty**](https://github.com/kiwi0fruit/knitty/blob/master/docs/knitty.md) . [Post di presentazione su Reddit](https://github.com/kiwi0fruit/knitty/blob/master/docs/reddit.md) .

Puoi usare:

- [Pandoc &gt;=2.6](https://pandoc.org/MANUAL.html#creating-jupyter-notebooks-with-pandoc) per esportare nei notebook .ipynb (opzionale: [installare Pandoc in Python](https://github.com/kiwi0fruit/py-pandoc) ),
- [ipynb-py-converter](https://github.com/kiwi0fruit/ipynb-py-converter) per convertire .ipynb in .py da utilizzare con Knitty.

## Installareppp pp

Installa come parte di [Pandoctools](https://github.com/kiwi0fruit/pandoctools) : interfaccia comoda e pronta all'uso.

Richiede Python 3.6+ ma è possibile avere altre versioni tramite i kernel Jupyter poiché Knitty può utilizzare qualsiasi kernel installato.

Via conka:bkkas;ldkas;dlkasd;lkas;dlkasd;lkasd;lkasd;lk

```bash
conda install -c defaults -c conda-forge knitty
```

Tramite pip:

```bash
pip install knitty
```

#### Kernel di Jupyter

Consultare ulteriori informazioni importanti su come [**installare i kernel Jupyter negli ambienti Conda**](https://github.com/kiwi0fruit/pandoctools/blob/master/docs/tips.md) . Per informazioni principali su come installare i kernel Jupyter, consultare la documentazione di Atom/Hydrogen (ad esempio).

#### Atomo/Idrogeno

Knitty è molto più efficace se utilizzato con un ambiente come Atom/Hydrogen. Per maggiori dettagli, consulta [la pagina "Migliore esperienza Python/Jupyter/PyCharm + generazione di report con filtri Pandoc"](https://github.com/kiwi0fruit/pandoctools/blob/master/docs/best_python_jupyter_pycharm_experience.md) . Puoi anche provare l'interfaccia **di VS Code** per Jupyter da [vscode-python](https://github.com/Microsoft/vscode-python) invece di Atom/Hydrogen. Consiglio vivamente di considerare ipynb semplicemente come un formato di output come il PDF (seppur dinamico e ricco di dettagli), anziché come un formato principale o intermedio.

## Esempo

Potete vedere esempi di utilizzo di Knitty [**qui**](https://github.com/kiwi0fruit/pandoctools/tree/master/examples) . Viene utilizzato insieme ad altri strumenti CLI tramite Pandoctools, ma la gestione delle celle e le opzioni sono di Knitty.

Vedere anche [l'esempio dalla documentazione di Knitty](https://github.com/kiwi0fruit/knitty/blob/master/docs/knitty.md#17-example-files-in-code-cells-mode) .

## Utilizzo

Puoi usare Knitty come filtro Pandoc standard:

```bash
cat doc.md | pre-knitty | pandoc --filter knitty -o doc.ipynb
```

e specificare un sottoinsieme di opzioni di Knitty nei metadati: `self_contained: True` , `standalone: True` . Ma in questo modo non è possibile passare da Markdown a RST, ad esempio.

In alternativa, puoi impostare tutte le opzioni di Knitty (incluse quelle nei metadati) usandolo come filtro Pandoc con più argomenti. Knitty è progettato per essere utilizzato nei profili bash di Pandoctools (quindi la sua interfaccia a riga di comando è suddivisa), ma puoi facilmente usarlo in modo indipendente. A questo scopo, dovresti solo **salvare e modificare** lo script della shell. Di seguito è riportato un esempio di Bash. Se usi Windows, consiglio vivamente di installare [Git insieme a Bash](https://git-scm.com/downloads) .

`./metadata.yaml` :

```yaml
---
kernels-map:
  r: ir
  py: python
styles-map:
  py: python
comments-map:
  py: ['#', "r'''", "'''", "'''", "'''", "r\"\"\"", "\"\"\"", "\"\"\"", "\"\"\""]
  js: ["//", "/*", "*/"]
  ts: ["//", "/*", "*/"]
  r: ['#', "'", "'", "\"", "\""]
...
```

`./knitty` :

```bash
#!/bin/bash
here="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
export LANG=C.UTF-8
export PYTHONIOENCODING=utf-8

in="$1"
yml="$here/metadata.yaml"
R=(-f markdown)
W=(-t html --standalone --self-contained)

t="$(pandoc-filter-arg "${W[@]}")"
cat "$in" |
pre-knitty "$in" --yaml "$yml" |
cat "$yml" <(printf "\n\n") - |
pandoc "${R[@]}" -t json |
knitty $t "$in" "${R[@]}" "${W[@]}" |
pandoc -f json "${W[@]}" -o "$in.html"
```

( `$t` è un argomento che Pandoc passa ai suoi filtri).

Quindi usalo come `./knitty /path/to/doc.py` che salverebbe `/path/to/doc.py.html` .

#### Lotto

Se non ti piace Bash, di seguito è riportato un esempio batch per Windows (vedi [setvar](https://github.com/kiwi0fruit/knitty/blob/master/examples/setvar.bat) ):

```bat
chcp 65001 > NUL
set PYTHONIOENCODING=utf-8
```
