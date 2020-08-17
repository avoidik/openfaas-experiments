# OpenFaas experiment

Based on [walkthrough](https://medium.com/@yankee.exe/deploy-your-serverless-python-function-locally-with-openfaas-in-kubernetes-c3b564e6fd18) and optimized for Windows

## How to run

* fetch arkade `curl -fsSL https://dl.get-arkade.dev | sudo sh`
* fetch kubectl `arkade get kubectl`
* fetch faas-cli `arkade get faas-cli`
* deploy k8s cluster with kind `./kind-with-registry.sh` (update docker machine ip if needed)
* deploy openfaas on kind `arkade install openfaas`
* list available openfaas templates `faas-cli template store list`
* fetch template `faas-cli template store pull python3-flask`
* init new openfaas project `faas-cli new pydict --lang python3-flask-debian`
* write function code & update requirements
* retrieve openfaas password `echo "Password: $(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)"`
* forward port `kubectl port-forward -n openfaas svc/gateway 8080:8080 &`
* build image `faas-cli build -f pydict.yml`
* push image `faas-cli push -f pydict.yml`
* deploy image `faas-cli deploy -f pydict.yml -g http://127.0.0.1:8080`
* verify deployment `kubectl get pods -n openfaas-fn`
* test function `echo "love" | faas-cli invoke pydict`
* open web portal `http://localhost:8080/ui/` (login - admin, password from above)

build/push/deploy might be executed in one go with `faas-cli up -f pydict.yml`

## References

https://github.com/alexellis/arkade

https://www.openfaas.com/

https://github.com/kubernetes-sigs/kind
