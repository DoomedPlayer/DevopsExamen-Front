# 🚚 Innovatech Chile - Frontend Web App (Examen Final)

Este repositorio contiene la aplicación cliente (Frontend) para la plataforma de gestión logística de Innovatech Chile. Proporciona una interfaz gráfica para que los operadores interactúen con los microservicios de Ventas y Despachos, permitiendo visualizar órdenes de compra, asignar transportes y actualizar los estados de entrega en tiempo real.

## ⚙️ Arquitectura, Contenedorización y Despliegue
La aplicación está construida con React (Vite) y empaquetada siguiendo buenas prácticas de contenedorización.
* **Multi-stage Build:** El `Dockerfile` utiliza una primera etapa con Node.js (`node:18-alpine`) para instalar dependencias y compilar la aplicación, y una segunda etapa minimalista con Nginx (`nginx:alpine`) para servir los archivos estáticos de forma eficiente y segura.
* **Despliegue:** La imagen final se despliega en un clúster de Amazon Elastic Kubernetes Service (EKS). El acceso público se expone mediante un **Application Load Balancer (ELB)** configurado nativamente por Kubernetes a través de un objeto tipo `Service`.

## 🚀 Instalación y Ejecución Local

### Prerrequisitos
* Node.js (v16 o superior)
* npm o yarn

### Pasos
1. Clonar el repositorio del examen:
   ```bash
   git clone https://github.com/DoomedPlayer/DevopsExamen-Front.git
   
2. Instalar las dependencias:
   ```bash
   npm install
3. Configurar variables de entorno:
Crea un archivo .env en la raíz del proyecto y configura las URLs de los microservicios (en desarrollo apuntan a localhost, en producción a los Load Balancers de AWS):  
   ```bash
   VITE_URL_DESPACHOS=http://localhost:8081
   VITE_URL_VENTAS=http://localhost:8082

4. Iniciar el servidor de desarrollo:
   ```bash
   npm run dev
   
## 🔄 Integración y Despliegue Continuo (CI/CD)
El proyecto cuenta con un flujo de GitHub Actions automatizado. Cada vez que se realiza un git push a la rama principal:
1. Build & Config: Durante el proceso de construcción, las URLs definitivas de los Load Balancers de AWS se inyectan dinámicamente como variables ARG y ENV (ej. VITE_URL_VENTAS) utilizando GitHub Secrets, evitando exponer las rutas reales en el código fuente.
2. Dockerize: Se construye la imagen Docker del Frontend empaquetando el código compilado dentro del servidor Nginx.
3. Push: Se publica la imagen en un repositorio privado de Amazon ECR, versionada mediante el hash del commit para asegurar trazabilidad.
4. Deploy: Se ejecuta kubectl set image para actualizar el objeto Deployment en el clúster EKS de AWS, logrando un despliegue sin interrupciones (Zero-Downtime Deployment). Las credenciales de autenticación hacia AWS están protegidas de forma estricta mediante GitHub Secrets.
