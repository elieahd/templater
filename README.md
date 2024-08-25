# Templater

`elieahd/templater` is a GitHub Action that simplifies the process of creating files based on existing templates.

By using this action, you can generate files dynamically by easily substituting `{{variables}}` in the template with actual values.

## Inputs
| input       | required | description                                                                 |
|-------------|----------|-----------------------------------------------------------------------------|
| `template`  | ✅        | Path to the template file relative to the repository root                   |
| `file`      | ✅        | Path where the new file should be created, relative to the repository root. |
| `variables` | ✅        | A list of key-value pairs for variable substitution in the template.        |

## How It Works

1. **Template File**: Define your template file with placeholders for variables. Placeholders should be in the format `{{VARIABLE_NAME}}`.
2. **GitHub Action Configuration**: Configure the action in your workflow file to specify the template, output file, and variables.
3. **File Generation**: The action will replace placeholders in the template with provided values and create the new file at the specified location.

# Examples of usages

## Example 01 : Hello world!
Create `my-hello-world-template.md` template file under `templates` directory:

```md
# Hello {{first_name}}!
Wow, {{first_name}} {{last_name}} have generated their first file from a template!!
```

Configure github action workflow 

```yaml
name: Generating a file using `elieahd/templater`
jobs:
  generate:
    steps:
      - uses: actions/checkout@v4
      - uses: elieahd/templater@v1
        with:
          template: 'templates/my-hello-world-template.md'
          file: 'outputs/hello-world.md'
          variables: 'first_name=John last_name=Doe'
      - run: cat outputs/first-template-new.md
```

Which will generate the file `hello-world.md` under `outputs` directory : 
```md
# Hello John!
Wow, John Doe have generated their first file from a template!!
```

## Example 02 : Setting image tag in Kubernetes manifest file in deployment pipeline

Template file : `k8s/deployment-template.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
  labels:
    app: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: backend:{{image_tag}}
        ports:
        - containerPort: 80
```

Setting image tag (for example to github sha) in the deployment github workflow

```yaml
name: Deploying app 
on: push
jobs:
  generate:
    steps:
      - uses: actions/checkout@v4
      ## docker build/push with tag ${{ github.sha }}
      - uses: elieahd/templater@v1
        with:
          template: 'k8s/deployment-template.yaml'
          file: 'k8s/deployment.yaml'
          variables: "image_tag=${{ github.sha }}"
      ## Deploy (kubectl apply ...)
```

# Contributing
We welcome contributions! If you have suggestions, improvements, or bug fixes, please open an issue or submit a pull request.