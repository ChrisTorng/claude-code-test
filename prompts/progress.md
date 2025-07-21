Claude Code/Claude Code Router/OpenRouter/DeepSeek: R1 0528 (free):

1. Create a simple Python FastAPI with recommend tools like uv/pre-commit/mkdocs/GitHub Actions/podman/ruff/pytest

```bash
sudo apt install python3.12-venv
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn
pip install pytest
git init
echo "dist/\nvenv/" > .gitignore
pip install pre-commit ruff mkdocs
sudo apt install pipx
pipx install poetry

cat > .pre-commit-config.yaml <<EOL
repos:
  - repo: https://github.com/pre-commit/mirrors-ruff
rev: v0.4.3
hooks:
  - id: ruff
EOL

pre-commit install
mkdir app && touch app/main.py

echo "from fastapi import FastAPI
app = FastAPI()
@app.get('/')
def read_root():
    return {'message': 'Hello World'}" > app/main.py

uvicorn app.main:app --reload

mkdir -p .github/workflows && touch .github/workflows/main.yml
cat > .github/workflows/main.yml <<EOL
name: CI
on: [push]
jobs:
test:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v3
- run: pip install -r requirements.txt
- run: pytest
EOL

cat >> .pre-commit-config.yaml <<EOL
repos:
    - repo: https://github.com/pre-commit/pre-commit-hooks
rev: v4.0.1
hooks:
        - id: check-yaml
    - id: end-of-file-fixer
    - id: trailing-whitespace
    - repo: https://github.com/psf/black
rev: 24.2.0
hooks:
        - id: black
    - repo: https://github.com/charliermarsh/ruff-pre-commit
rev: v0.1.9
hooks:
        - id: ruff
args: [--fix]
EOL

echo "fastapi
uvicorn
pytest
pytest-cov
ruff
pre-commit
mkdocs" > requirements.txt

mkdir tests && touch tests/test_main.py

echo "from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_read_main():
    response = client.get('/')
    assert response.status_code == 200
    assert response.json() == {'message': 'Hello World'}" > tests/test_main.py

pip install httpx
python -m pytest -v

pre-commit run --all-files
```
