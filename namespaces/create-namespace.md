# Create a Namespace

While the `default` namespace is created automatically, it is a good practice to create a custom namespace to start
with.

## Resource Definition

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: web-app-namespace
```
