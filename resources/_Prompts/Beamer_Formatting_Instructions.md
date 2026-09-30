# TeX Formatting Prompt for an Existing Paper-Summary Deck

Use this prompt together with other inputs specified in the next section.

This prompt aims to control how new slide content should be inserted and formatted.

## Formatting prompt

- You are editing an existing LaTeX Beamer file for an economics theory drafting task.
- The file already contains the correct preamble, packages, commands, colors, fonts, theme, title metadata, title frame, outline behavior, list spacing, appendix marker, and document ending.
- Add or revise the substantive frames while preserving that existing template.

### Scope

- Edit the supplied TeX file in place.
- Do not change or reproduce the preamble.
- Insert main-deck frames after the title frame and before `\appendix`.
- Insert supplementary frames after `\appendix` and before `\end{document}`.

### Do not hard-wrap prose

- **Do not insert source-code line breaks merely to satisfy a maximum line length such as 80, 100, or 120 characters.**
- Keep the complete prose of each `\item` on one physical source line. If an item has a nested list, keep the parent item's prose on one line and place the nested environment on following lines.
- Add source-code line breaks only at meaningful TeX boundaries, such as between commands, environments, list items, display equations, table rows, figure blocks, or separate paragraphs.
- Do not use `\\`, `\newline`, or `\linebreak` to wrap ordinary prose. Use them only when a deliberate semantic break is required.
- Do not split one sentence across multiple source lines through comments or indentation.

### Organize the TeX body

- Use `\section{...}` and `\subsection{...}` when the separate paper-summary instructions call for substantive divisions.
- Do not create manual outline frames. The existing template generates section and subsection outline frames automatically.
- Use descriptive, unique labels such as `\label{Frame_Data}`, `\label{Eq_MainReg}`, `\label{Fig_EventStudy}`, and `\label{Tab_MainResults}`.
- Put a frame label on the same source line as its frame title: `\frametitle{Data and sample} \label{Frame_Data}`.
- Indent nested TeX blocks consistently with four spaces.
- Use blank lines to separate structural blocks within a frame, following the style already present in the file.
- Do not use Beamer overlays such as `\pause`, `<2->`, or `\only`.

### Format text frames

- Prefer `itemize` for the main content of ordinary frames.
- Use nested `itemize` when the hierarchy is meaningful.
- Keep each bullet focused on one point.
- Split an overcrowded frame into two or more frames before reducing the font size.
- Do not use `\tiny`, `\scriptsize`, negative vertical spacing, or compressed line spacing to force excessive text onto a frame.
- Use the template's existing emphasis commands sparingly: `\assumptions{}`, `\defs{}`, `\procedures{}`, `\interpretations{}`, and `\important{}`; for assumptions, definitions, procedures, interpretations, and important contents, respectively.
- Do not color full paragraphs. Use color cautiously to emphasize a short phrase, key interpretations, contrast, or warning.

Use this basic pattern:

```tex
\begin{frame}
    \frametitle{Informative frame title} \label{Frame_DescriptiveLabel}

    \begin{itemize}
        \item First complete point.
        \begin{itemize}
            \item Supporting detail.
        \end{itemize}
        \item Second complete point.
    \end{itemize}

\end{frame}
```

### Format equations

- Use `$...$` for inline math.
- Use `\begin{equation} ... \end{equation}` with a descriptive label when an equation needs a number or will be referenced.
- Use `$$ ... $$` for an unnumbered display equation.
- Place display equations on their own structural lines. The no-hard-wrapping rule applies to prose, not to readable mathematical alignment.
- Use `aligned`, `align`, or another already available mathematics environment when an equation has meaningful multiple lines.

Use this basic pattern:

```tex
\begin{frame}
    \frametitle{What the equation establishes} \label{Frame_Estimation}

    \begin{itemize}
        \item Introduce the equation and state its purpose.
        \begin{equation}
            \label{Eq_MainReg}
            y_{it} = \alpha + \beta x_{it} + \varepsilon_{it}
        \end{equation}
        \item Define the important terms in one-line bullets.
    \end{itemize}

\end{frame}
```

## Final checks

- Confirm that the preamble, title metadata, title frame, template definitions, `\appendix`, and `\end{document}` remain intact.
- Confirm that main frames appear before `\appendix` and supplementary frames appear after it.
- Confirm that every prose paragraph and every `\item` remains on one physical source line and has not been wrapped to a fixed character width.
- Confirm that all braces and environments are balanced.
- Confirm that all labels are descriptive and unique.
- Confirm that every referenced asset exists and every citation key is available.
- Remove all placeholder text from the actual deck.
- Compile the TeX file if a compiler is available and fix compilation errors.
- Inspect the compiled PDF when possible. Fix clipping, overcrowding, tiny text, awkward visual wrapping, missing figures, poor alignment, and excessive empty space.
