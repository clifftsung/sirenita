# Per‑Chapter Bibliographies in LaTeX

This guide shows how to keep a separate `.bib` file for each chapter and print a bibliography per chapter. Two common setups are covered:

- biblatex + biber (recommended)
- BibTeX (with `chapterbib`, optionally `natbib`)

Pick one toolchain and stick to it (don’t mix BibTeX and biber).

## biblatex + biber (recommended)

### Preamble

```tex
\usepackage[backend=biber,refsection=chapter,defernumbers=true]{biblatex}
```

- `backend=biber`: uses biber instead of BibTeX.
- `refsection=chapter`: isolates citations per chapter.
- `defernumbers=true`: restarts numbering per chapter (optional).

### Per‑chapter structure

Wrap each chapter in a `refsection` that points to that chapter’s `.bib` file and print a sub‑bibliography at the end of the chapter:

```tex
\begin{refsection}[chap1.bib]
  \chapter{Chapter One}
  Some text \autocite{keyA}.
  \printbibliography[heading=subbibliography]
\end{refsection}

\begin{refsection}[chap2.bib]
  \chapter{Chapter Two}
  More text \autocite{keyB}.
  \printbibliography[heading=subbibliography]
\end{refsection}
```

Notes:

- You can customize the heading: `\printbibliography[heading=subbibliography,title={References for Chapter \thechapter}]`.
- If you prefer, you may also `\addbibresource{chap1.bib}` in the preamble and omit the optional argument to `refsection`. Listing the `.bib` files on the `refsection` is clearer for per‑chapter separation.

### Optional global bibliography at the end

To also produce an overall bibliography that combines all chapter `.bib` files:

```tex
\clearpage
\begin{refsection}[chap1.bib,chap2.bib]
  % Include all entries from those files; remove \nocite{*} to include only cited items
  \nocite{*}
  \printbibliography[title={Overall References}]
\end{refsection}
```

### Compile

- latexmk: `latexmk -pdf -use-biber main.tex`
- Manually: `pdflatex → biber → pdflatex → pdflatex`

Common pitfalls:

- Make sure your editor/build uses biber (not BibTeX).
- Ensure `biber` is installed (TeX Live/MiKTeX) if compiling locally.

## BibTeX (+ chapterbib)

This approach requires each chapter to be brought in with `\include{...}` so BibTeX can see chapter‑level `.aux` files.

### Preamble (main.tex)

```tex
\usepackage[sectionbib]{chapterbib}
% Optional if you want natbib citation commands
% \usepackage[numbers]{natbib}
```

### Main file (main.tex)

```tex
\documentclass{book}
\usepackage[sectionbib]{chapterbib}
\begin{document}
\include{chap1}
\include{chap2}
\end{document}
```

### Chapter files

Each chapter lives in its own file (e.g., `chap1.tex`) and declares its own bibliography style and database:

```tex
% chap1.tex
\chapter{Chapter One}
Text with a citation \cite{keyA}.
\bibliographystyle{plain}
% or: \bibliographystyle{plainnat} if using natbib
\bibliography{chap1}
```

```tex
% chap2.tex
\chapter{Chapter Two}
Another citation \cite{keyB}.
\bibliographystyle{plain}
\bibliography{chap2}
```

Each chapter references its own `.bib` file (e.g., `chap1.bib`, `chap2.bib`).

### Compile

1. `pdflatex main`
2. `bibtex chap1` and `bibtex chap2` (run BibTeX on each included chapter name)
3. `pdflatex main`
4. `pdflatex main`

Common pitfalls:

- Use `\include{...}` (not `\input{...}`) so that `chapX.aux` files exist for BibTeX.
- Run BibTeX for each chapter (`bibtex chapX`).

## Tips

- File layout: keep `chapX.bib` files alongside chapter `.tex` files or in a dedicated `bib/` folder and reference with relative paths (e.g., `bib/chap1.bib`).
- Don’t mix toolchains: choose either biblatex+biber or BibTeX.
- For per‑chapter numbering styles or headings, use biblatex options like `defernumbers=true` or customize `\defbibheading{subbibliography}{...}`.

## Minimal biblatex Example

```tex
\documentclass{book}
\usepackage[backend=biber,refsection=chapter,defernumbers=true]{biblatex}
\begin{document}
\begin{refsection}[chap1.bib]
\chapter{A}
Text \autocite{a}.
\printbibliography[heading=subbibliography]
\end{refsection}

\begin{refsection}[chap2.bib]
\chapter{B}
Text \autocite{b}.
\printbibliography[heading=subbibliography]
\end{refsection}
\end{document}
```

Where `chap1.bib` contains entry with key `a`, and `chap2.bib` contains key `b`.

