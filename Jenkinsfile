template = '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: packer
  name: packer
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: hashicorp/packer
    name: packer
    '''

def buildNumber = env.BUILD_NUMBER

properties([
    parameters([
        choice(choices: ['us-east-1', 'us-east-2', 'us-west-1', 'us-west-2'], name: 'region')
        ])
        ])

podTemplate(cloud: 'kubernetes', label: 'packer', showRawYaml: false, yaml: template) {
    node("packer"){
        container("packer"){

        withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            withEnv(["AWS_REGION=${params.region}"]) {
            
            stage("Git Clone"){
                git branch: 'main', url: 'https://github.com/kaizenacademy/jenkins-packer.git'
            }
            
            stage("Packer"){
                sh "packer build -var 'jenkins_build_number=${buildNumber}' packer.pkr.hcl"
            }
            }
        }
        }
    }
}
