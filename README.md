 cI name: Python
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-lates
    teps:
      - uses: actions/checkout@
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"
      - name: Install dependencie
        run: |
          python -m pip install --upgrade pi
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
      - name: Run tests
        run: |
          if [ -d tests ]; then pytest -q; else echo "No tests"; fi
