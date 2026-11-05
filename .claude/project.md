# Project facts for devkit commands

The flow commands read this file. Claude Code does not load it in a normal
session. Each fact is one line: a dash, the key, a colon, and the value in
backticks. The keys are in
<https://github.com/unibrain1/devkit/blob/main/docs/project-md.md>.

- test: `pytest tests/ --cov=custom_components/fireboard --cov-fail-under=80`
- test_quick: `pytest tests/ -q`
- lint: `flake8 custom_components/fireboard tests/ && black --check custom_components/fireboard tests/ && isort --check-only custom_components/fireboard tests/`
- format: `black custom_components/fireboard tests/ && isort custom_components/fireboard tests/`
- deploy: see "Releasing" in CLAUDE.md
- plans: `docs/plans`
