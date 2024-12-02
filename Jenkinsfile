pipeline {
  agent {
    // TBE:Jenkins エージェントのラベルを指定する
    label 'linux && understand'
  }
  environment {
    GITSERVICE = 'github'
    GITHUB_CRED = credentials('GITEA_CRED')
    // TBE: Gitea の URL を指定する
    GITHUB_URL  = "https://github.com/tmx-tomoyoshi/fastgrep"
    
    // TBE:使用するストレージのアクセス情報を指定する
    STORAGESERVICE = 'aws-s3'
    AWS_S3_BUCKET_NAME = 'ltxund-jenkins-storage'
    AWS_REGION = 'ap-northeast-1'
    AWS = credentials('AWS_CRED')
    // STORAGESERVICE = 'nexus'
    // NEXUS_URL = "http://172.20.128.8"
    // NEXUS_CREDENTIALS_FILE = credentials('NEXUS_CREDENTIALS_FILE')
  }
  options {
    buildDiscarder logRotator(numToKeepStr: '10')
  }
  stages {
    stage('解析') {
      when {
          anyOf {
              branch 'main'
              changeRequest target: 'main'
          }
      }
      steps {
        sh 'env'
        sh './understand/analyze.sh --upload'
      }
    }
    stage('PRレビュー') {
      when {
        changeRequest target: 'main'
      }
      steps {
        sh '''
          ./understand/generate-graphs.sh > review-comment.txt
          ./understand/review-pr.sh review-comment.txt
        '''
      }
    }
  }
  post {
    cleanup {
      sh './understand/clean.sh'
    }
  }
}
