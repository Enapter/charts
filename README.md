# Rollout release

## New KeyDB release example

### Package new release in .tgz

```console
helm package ./keydb/ --destination ./.deploy/
```

### Upload the release to Github

```console
helm-cr upload --config ~/.cr.yaml
```

### Update index.yaml

```console
git checkout gh-pages
helm-cr index --config ~/.cr.yaml -i ./index.yaml -c https://enapter.github.io/charts/
```
