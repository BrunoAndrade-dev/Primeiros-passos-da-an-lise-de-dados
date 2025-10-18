## Quick orientation for AI coding agents

This is a small, notebook-first data analysis repository. The primary artifacts are Jupyter notebooks and a single Excel dataset. Keep changes minimal and focused on reproducible analysis; prefer edits to notebooks and small helper scripts over large refactors.

### High-level architecture / data flow
- Data source: `dados/Vendas.xlsx` (single source of truth used by notebooks).
- Analysis lives in `Notebook/codigo.ipynb` (main analysis). A secondary notebook exists at `Read_me/read.ipynb`.
- Typical flow: open the notebook -> load Excel with pandas -> transform/aggregate in-place -> display results and charts in notebook cells.

### Key files to inspect
- `Notebook/codigo.ipynb` — loads the Excel workbook with an absolute path and performs pandas operations. Example found in the notebook:

  `tabela = pd.read_excel(r'C:\\Users\\Brunin\\Documents\\AnaliseIntrodutóriaSEAD_UFMA\\dados\\Vendas.xlsx')`

  And an aggregation attempt (incorrect indexing in the original):

  `faturamento_lojas = tabela['ID Loja']['Valor Final'].groupby('ID Loja')`

  Preferred, equivalent pattern used elsewhere (or when fixing):

  `faturamento_lojas = tabela.groupby('ID Loja')['Valor Final'].sum()`

- `dados/Vendas.xlsx` — dataset used by notebooks. Edits to this file will change analysis outputs.

### Repo-specific conventions & patterns
- Notebook-first: the repository contains no scripts, tests, or build tooling. Expect changes to be primarily to `.ipynb` cells.
- Absolute paths are used in `Notebook/codigo.ipynb`. When making changes, prefer converting to relative paths or using pathlib to improve portability, but only change paths when you also update the notebook cells that depend on them.
- Pandas usage: data loading via `pd.read_excel` and in-notebook aggregations with `groupby` and `sum` are the main transformation patterns.

### Running / debugging (developer workflow)
For an interactive session on Windows PowerShell (recommended):

```powershell
# create a venv and activate (recommended)
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# install minimal requirements
pip install pandas openpyxl

# open the repository in VS Code or Jupyter and run cells in `Notebook/codigo.ipynb`
``` 

Notes:
- The notebooks currently rely on the Excel file at `dados/Vendas.xlsx`. Ensure the working directory is the repository root or update the path in the notebook to a relative path: `pd.read_excel('..\\dados\\Vendas.xlsx')` or `pd.read_excel('dados\\Vendas.xlsx')` depending on your working directory.

### What an AI agent should do first
1. Open `Notebook/codigo.ipynb` and run the first cells to confirm environment and data load succeed.
2. If a code cell uses an absolute path, prefer leaving it intact unless you also update surrounding cells and confirm the notebook still runs end-to-end.
3. When fixing pandas expressions, prefer idiomatic groupby usage: `tabela.groupby('ID Loja')['Valor Final'].sum()` instead of chaining bracket-accessors the way the notebook currently shows.

### Concrete examples to reference in edits
- Fix aggregation (cell near the top of `Notebook/codigo.ipynb`):

  Replace the original

  `faturamento_lojas = tabela['ID Loja']['Valor Final'].groupby('ID Loja')`

  With the working aggregation:

  `faturamento_lojas = tabela.groupby('ID Loja')['Valor Final'].sum()`

- Use `display(...)` or plain expression in notebook cells to show results. The notebooks use `display(faturamento_lojas)` after aggregations.

### Integration points & dependencies
- External dependency: pandas (and `openpyxl` for Excel). There is no CI, package manifest, or tests in the repo.

### Editing rules for AI agents
- Make minimal, testable edits. Run the notebook cells locally and confirm outputs change as expected before committing.
- When editing notebooks, update only the cells required for the fix and avoid wholesale reformatting of cells or outputs.
- Document any path, environment, or dependency changes in the same notebook (a new markdown cell near the top is acceptable).

### When to ask the human
- Ask if you need to change the dataset (`dados/Vendas.xlsx`) or create new scripts. The repo owner likely expects lightweight notebook edits and analysis-only changes.

---
If any section above is unclear or you'd like more detail (e.g., a recommended minimal `requirements.txt` or converting notebooks to scripts), tell me which part to expand and I'll iterate.
