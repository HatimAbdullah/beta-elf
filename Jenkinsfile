pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: elf-pod
  labels:
    service_name: elf-service
    service_type: REST
spec:
  containers:
  - name: helm
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
"""
    }
  }
  environment {
    DOCKER_NAMESPACE = 'lordblackfish'
    SERVICE_NAME = 'mother-service'
  }
  stages {
      stage("Build") {
          steps {
              container('helm') {
                  sh """
		      kubectl apply -f namespace.yaml
                      helm repo add elastic https://helm.elastic.co
                      helm repo add fluent https://fluent.github.io/helm-charts
                      helm repo update
                      helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf -f values.yaml
                      helm install fluent-bit fluent/fluent-bit --namespace=elf -f values.yaml
                      helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort -f values.yaml
                      kubectl run random-logger --image=chentex/random-logger -n elf
                  """
              }
          }
      }
  }
}
