# appconduits

IN PROGRESS

## Helm package/update

```
helm package . -d repo/charts/
helm repo index repo/
```

# Using

```
helm repo add bitsofinfo-appconduits https://raw.githubusercontent.com/bitsofinfo/appconduits/master/repo
```

```
# requirements.yaml
dependencies:
- name: appconduits
  version: "1.0.0"
  repository: "https://raw.githubusercontent.com/bitsofinfo/appconduits/master/repo"
```
