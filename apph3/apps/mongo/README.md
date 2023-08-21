# extracted

this mongo files from Nana Janashia's repo

<https://gitlab.com/nanuchi/youtube-tutorial-series/-/tree/master/demo-kubernetes-components>

<https://youtu.be/X48VuDVv0do>

### give a URL to external service in minikube

use `minikube service mongo-express-service` to expose a public service (a Service k8s object that has LoadBalancer type -by default all are ClusterIP- )

command:

```sh
minikube service mongo-express-service
```

output:

```sh
W0820 19:27:39.104980   14176 main.go:291] Unable to resolve the current Docker CLI context "default": context "default" does not exist
|-----------|-----------------------|-------------|---------------------------|
| NAMESPACE |         NAME          | TARGET PORT |            URL            |
|-----------|-----------------------|-------------|---------------------------|
| apph3     | mongo-express-service |        8081 | http://192.168.49.2:30000 |
|-----------|-----------------------|-------------|---------------------------|
🏃  Starting tunnel for service mongo-express-service.
|-----------|-----------------------|-------------|------------------------|
| NAMESPACE |         NAME          | TARGET PORT |          URL           |
|-----------|-----------------------|-------------|------------------------|
| apph3     | mongo-express-service |             | http://127.0.0.1:50272 |
|-----------|-----------------------|-------------|------------------------|
🎉  Opening service apph3/mongo-express-service in default browser...
❗  Porque estás usando controlador Docker en windows, la terminal debe abrirse para ejecutarlo.
```
