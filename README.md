# Genesis Documentation

1. Install Sphinx and other dependencies

```bash
# In Genesis-dev/
pip install -e ".[docs]"
```

2. Build the documentation and watch the change lively

```bash
# In docs/
rm -rf build/; make html; sphinx-autobuild ./source ./build/html
```

For github links for the time being must double check they link the right branch/commit