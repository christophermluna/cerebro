Jenkins X es una cli para provisionars clusters de Kubernetes con una serie de herramientas para que los developers se pongan a trabajar de manera rápida y sencilla.

Viene con Jenkins, Helm, integraciones con Github, etc

jx es la herramienta que usaremos para desplegar Jenkins X

# install cli
Mirar cual es la última release: https://github.com/jenkins-x/jx/releases
curl -L https://github.com/jenkins-x/jx/releases/download/v1.3.474/jx-linux-amd64.tar.gz | tar xzv
sudo mv jx /usr/local/bin


# install Jenkins X sobre un cluster de kubernetes ya montado
https://jenkins-x.io/getting-started/install-on-cluster/