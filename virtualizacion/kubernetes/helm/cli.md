helm search hub foo
  busca en el hub de helm
  no nos dice en que repo está para poder añadirlo, entrar en la web para mirarlo
helm search repo foo
  buscar charts en el repo publico
helm search hub foo
  no es muy útil, ya que tenemos que entrar a la web para coger el nombre del repo
  https://stackoverflow.com/questions/60994725/k8s-how-to-install-charts-from-the-helm-hub

Parece que como funciona el "hub" es un poco raro:
https://stackoverflow.com/questions/60994725/k8s-how-to-install-charts-from-the-helm-hub

Podemos buscar a mano en
https://artifacthub.io/


helm pull xxx
  es "fetch" en v2.x
  bajarnos el .tgz de un chart, en el current dir que estemos

helm pull redis/redis --version 0.1.1 --untar


helm template nombre chart > file.yml
  renderizar como va a quedar un chart
  podemos especificar solo un fichero con "-x fichero.yaml"

helm install NOMBRE somedir --set foo=bar,foobar=barfoo
helm install miredis stable/redis
  Para la v2: helm install somedir
  instalar un chart
  --name foo
    darle un nombre, si no, cogerá uno random
  --values=other.yaml
    usar otro fichero en vez del values.yml
  --description "asdad"
    crear la release con una descripción

helm upgrade releaseName chartPath
  --install: si no existe una releas con este nombre, crearla
  configmaps not overwritten: https://github.com/helm/helm/issues/5915
  no se modifican los configmaps. Hacerlo a mano, o borrar la release y redesplegar

helm lint mychart
  buscar problemas de linting

helm package mychart
  generar el .tgz



helm list --all
  muestra los charts desplegados
  sin --all solo muestra las "DEPLOYED"

helm status FOO
  estado de un deploy, fecha, namespace, status y el NOTES.txt


helm delete nombre
  borra los objectos, pero deja el chart como "DELETED"
  --purge para borrar todo



# Dependencias
helm dep up
helm dependency update
  bajar las deps definidas en requirements.yaml

requirements.yaml (CUIDADO! es .yaml no .yml)
dependencies:
  - name: apache
    version: 1.2.3
    repository: http://example.com/charts
  - name: mysql
    version: 3.2.1
    repository: "@stable"
    alias: new-subchart-2

Tambien se les pueden poner tags y conditions

https://github.com/helm/helm/blob/master/docs/charts.md#operational-aspects-of-using-dependencies
Al definir dependencias, cuando instalemos nuestro chart, también se instalarán las dependencias.


Podemos pasar variables desde el chart padre a los hijos en el fichero values.yml
mysql:
  max_connections: 100 # Sent to MySQL
  password: "secret"



# Plugins
https://github.com/appscode/chartify
Generate Helm charts from existing Kubernetes resources

Helmfile - Helmfile is a declarative spec for deploying helm charts

Monocular - Web UI for Helm Chart repositories

VIM-Kubernetes - VIM plugin for Kubernetes and Helm
