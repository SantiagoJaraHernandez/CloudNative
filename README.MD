# Hola Mundo Cloud Native

Este es un proyecto de ejemplo para demostrar la implementación de una aplicación Cloud Native usando Flask, Docker, Kubernetes y GitHub Actions.

## Requisitos

- Docker
- Kubernetes (Minikube o un clúster en la nube)
- Cuenta en Docker Hub
- Cuenta en GitHub

## Estructura del Proyecto

```
hola-mundo-cloud-native/
├── src/
│   ├── app.py
│   └── templates/
│       └── index.html
├── requirements.txt
├── Dockerfile
├── deployment.yaml
├── service.yaml
├── .github/workflows/
│   └── deploy.yaml
├── README.md
└── .gitignore
```

## Cómo Ejecutar el Proyecto

### Localmente con Docker

1. Construir la imagen Docker:
   ```bash
   docker build -t hola-mundo-cloud-native .
   ```

2. Ejecutar el contenedor:
   ```bash
   docker run -p 5000:5000 hola-mundo-cloud-native
   ```

3. Abrir en el navegador:
   ```
   http://localhost:5000
   ```

### Desplegar en Kubernetes

1. Aplicar los archivos de Kubernetes:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

2. Obtener la IP del servicio:
   ```bash
   kubectl get services
   ```

3. Abrir en el navegador:
   ```
   http://<EXTERNAL-IP>
   ```

## CI/CD con GitHub Actions

El proyecto incluye un workflow de GitHub Actions para construir la imagen Docker, subirla a Docker Hub y desplegarla en Kubernetes automáticamente.

### Configuración de Secrets en GitHub

Para que el workflow funcione correctamente, debes configurar los siguientes secrets en GitHub:

1. **DOCKER_USERNAME**: Tu nombre de usuario de Docker Hub.
2. **DOCKER_PASSWORD**: Tu contraseña de Docker Hub.
3. **KUBE_CONFIG**: El contenido completo de tu archivo `kubeconfig` (obtenido con `cat ~/.kube/config`).

#### Pasos para Configurar los Secrets:

1. Ve a tu repositorio en GitHub.
2. Haz clic en **Settings > Secrets and variables > Actions**.
3. Haz clic en **New repository secret**.
4. Agrega los secrets mencionados anteriormente.

### Workflow de GitHub Actions

El archivo `.github/workflows/deploy.yaml` contiene el workflow que se ejecuta cada vez que se hace un `push` a la rama `main`. Aquí está el contenido:

```yaml
name: Desplegar aplicación Cloud Native

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout código
      uses: actions/checkout@v2

    - name: Construir imagen de Docker
      run: docker build -t tu-usuario/hola-mundo-cloud-native:latest .

    - name: Iniciar sesión en Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

    - name: Subir imagen a Docker Hub
      run: docker push tu-usuario/hola-mundo-cloud-native:latest

    - name: Desplegar en Kubernetes
      run: |
        echo "${{ secrets.KUBE_CONFIG }}" > kubeconfig.yaml
        export KUBECONFIG=kubeconfig.yaml
        kubectl apply -f deployment.yaml
        kubectl apply -f service.yaml
```

## Tecnologías Usadas

- **Flask**: Framework de Python para construir la aplicación web.
- **Docker**: Para contenerizar la aplicación.
- **Kubernetes**: Para orquestar y desplegar la aplicación en un clúster.
- **GitHub Actions**: Para automatizar el proceso de CI/CD.

## Licencia

Este proyecto está bajo la licencia MIT. Ver [LICENSE](LICENSE) para más detalles.