# Contributing

## Setup

```bash
git clone <repository-url> # clone repo
cd css-dsml-fall2027
python -m venv .venv # create virtual env
```

Activate the environment:

```bash
# macOS/Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Download [The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset/) and place the required CSV files in `data/raw/`. For example:

```text
data/raw/movies_metadata.csv
data/raw/credits.csv
data/raw/keywords.csv
```

The `data/` folder is gitignored. Do not commit these files.

## Workflow

Update `main` and create your branch:

```bash
git switch main
git pull origin main
git switch -c <yourName>-<issueNumber>
```

Example:

```bash
git switch -c jasper-1
```

Complete the issue, then commit and push:

```bash
git add <files>
git commit -m "Complete issue #1"
git push -u origin <yourName>-<issueNumber>
```

On GitHub, open a pull request into `main`, include `Closes #<issueNumber>`, and request a supervisor’s review.

Do not commit datasets, virtual environments, trained models, or credentials.
