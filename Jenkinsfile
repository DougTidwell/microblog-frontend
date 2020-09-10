library 'cb-days@tf-overhaul'
def testPodYaml = libraryResource 'podtemplates/vuejs/vuejs-test-pod.yml'
pipeline {
  agent none
  options {
    skipDefaultCheckout true
    preserveStashes(buildCount: 10)
  }
  environment {
    repoOwner = "dtidwell-cbdemos"
    repository = "microblog-frontend"
    gcpProject = "core-flow-research"
  }
  stages('VueJS Test & Build')
  {
    stage('VueJS Tests') {
      agent {
        kubernetes {
          label 'nodejs'
          yaml testPodYaml
        }
      }
      steps {
        checkout scm
        container('nodejs') {
          sh '''
            yarn install
            yarn test:unit
          '''
        }
      }
    }
    stage('Build and Push Image') {
      steps {
        kanikoBuildPushGeneric("${repository}", "latest", "${gcpProject}/${repoOwner}") {
          checkout scm
        }
        echo "Pushed image to gcr.io/${gcpProject}/${repoOwner}/${repository}"
      }
    }
  }
}
