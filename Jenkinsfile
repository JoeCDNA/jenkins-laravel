pipeline {
  agent {
    docker {
      image 'jestefane/php:7.2'
    }

  }
  stages {
    stage('Build Dependencies') {
      parallel {
        stage('PHP') {
          steps {
            sh 'composer install'
            sh '''php -r "file_exists(\'.env\') || copy(\'.env.example\', \'.env\');"
php artisan key:generate'''
          }
        }
        stage('Javascript') {
          steps {
            sh 'yarn'
            sh 'yarn dev'
          }
        }
      }
    }
    stage('Tests') {
      steps {
        sh 'php ./vendor/phpunit/phpunit/phpunit --configuration phpunit.xml'
      }
    }
    stage('Archive') {
      steps {
        zip(zipFile: 'laravel-build.zip', dir: '.')
        ftpPublisher(masterNodeName: 'master-docker-node', alwaysPublishFromMaster: true, continueOnError: true, failOnError: true, publishers: [
                    [configName: 'NYCNS102 (Backup FTP)', transfers: [
                        [asciiMode: false, cleanRemote: false, excludes: '', flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'laravel-build.zip']
                      ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false]
                    ], paramPublish: [parameterName: ''])
      }
    }
    stage('Deploy') {
      steps {
        sshPublisher(publishers: [
          sshPublisherDesc(configName: 'NYCUB36T', transfers: [
            sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'buildz', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'laravel-build.zip')
          ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)
        ])
        sshPublisher(publishers: [
          sshPublisherDesc(configName: 'NYCUB36T', transfers: [
            sshTransfer(cleanRemote: false, excludes: '', execCommand: 'unzip laravel-build.zip', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'buildz', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')
          ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)
        ])
      }
    }
  }
  environment {
    HTTP_PROXY = 'http://10.216.0.249:8080/'
    HTTPS_PROXY = 'http://10.216.0.249:8080/'
    FTP_PROXY = 'ftp://10.216.0.248:1080/'
  }
}