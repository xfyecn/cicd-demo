podTemplate(label: 'cloud-native', containers: [
    containerTemplate(name: 'jnlp', image: '10.250.131.118:5000/jenkinsci/jnlp-slave:2.62', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins'),
    containerTemplate(name: 'docker', image: '10.250.131.118:5000/docker:1.13.1', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'kubectl', image: '10.250.131.118:5000/kubectl:1.7.0', command: 'cat', ttyEnabled: true)
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    configMapVolume(mountPath: '/root/.kube', configMapName: 'kube-config')
]){

  node ('cloud-native') {
    git(url: 'https://github.com/DanielXiao/cicd-demo.git', branch: 'master', credentialsId: 'github-token', changelog: true, poll: true)

    stage ('Build image') {
      container('docker') {
        sh "docker info"
        sh "docker version"
        sh "ls -la /home/jenkins"
      }
    }

    stage ('Push image to registry') {
      container('docker') {
        sh "docker info"
        sh "docker version"
        sh "ls -la /home/jenkins"
      }
    }

    stage ('End to end testing') {
      parallel (
        'End to End test 1': {
          container('kubectl') {
            sh "kubectl create -f k8s-config/guestbook-e2e-1.yaml"
            println "Do some testing"
            sh "sleep 300"
          }
        },
        'End to End test 2': {
          container('kubectl') {
            sh "kubectl create -f k8s-config/guestbook-e2e-2.yaml"
            println "Do some testing"
            sh "sleep 300"
          }
        }
      )
    }

    stage('Manual promotion') {
        input 'Do you approve to promote build to production?'
    }

    stage ('Rolling update production') {
      container('kubectl') {
        sh "kubectl version"
        sh "ls -la /home/jenkins"
      }
    }

  }
}