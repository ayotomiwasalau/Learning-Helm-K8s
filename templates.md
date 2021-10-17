Templates are at the heart of Helm charts, and they make up a majority of
the files and content of a chart. These are the files that live within the
`templates` directory. Helm will render the templates and send them to
Kubernetes when you run commands like helm install and helm
upgrade.

### The Template Syntax

### Pipelines

A pipeline is a sequence of commands, functions, and variables chained
together. The value of a variable or the output of a function is used as the
input to the next function in a pipeline. The output of the final element of a
pipeline is the output of the pipeline. The following illustrates a simple
pipeline:
`character: {{ .Values.character | default "Sylvester" | quote }}`

From the `values.yml`,this how resource looks like

```yaml
resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

On the `deployment.yaml` file, the command below is converting the

```yaml
resources: { { - toYaml .Values.resources | nindent 12 } }
```

1. resource values to yaml
1. `nindent` is used instead of the indent function for readability. The indent function does not add a newline at the beginning.
1. `- `is used with {{ to remove all the whitespace up to the

### Variables

Creating and initializing a variable has a special syntax through the use of `:=`, like the following example:
`{{ $var := .Values.character }}`

`character: {{ $var | default "Sylvester" | quote }}`

#### if/else/with statement

```yaml
 {{- with .Values.ingress.annotations }}
 annotations:
    {{- toYaml . | nindent 4 }}
 {{- end }}
```

#### Looping

```yaml
# values.yml file
# An example list in YAML
characters:
  - Sylvester
  - Tweety
  - Road Runner
  - Wile E. Coyote
# An example map in YAML
products:
  anvil: They ring like a bell
  grease: 50% slippery
  boomerang: Guaranteed to return
```

```yaml
characters:
{{- range .Values.characters }}
 - {{ . | quote }}
{{- end }}

products:
{{- range $key, $value := .Values.products }}
 - {{ $key }}: {{ $value | quote }}
{{- end }}
```

#### Debugging templates
1. Use dry run
```shell
helm install cookingapp flaskapp --dry-run
```
1. Getting Installed Manifests
```shell
helm get manifest cookingapp
```
1. Lint your chats
```shell
helm lint flaskapp
```
