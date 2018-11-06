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
        zip archive: true, dir: '', glob: '', zipFile: '${JOB_NAME}-${BUILD_NUMBER}.zip'
        ftpPublisher alwaysPublishFromMaster: false, continueOnError: false, failOnError: false, publishers: [
          [configName: 'NYCNS102 (Backup FTP)', transfers: [
            [
              asciiMode: false,
              cleanRemote: false,
              excludes: '',
              flatten: false,
              makeEmptyDirs: true,
              noDefaultExcludes: false,
              patternSeparator: '[, ]+',
              remoteDirectory: '${JOB_NAME}',
              remoteDirectorySDF: false,
              removePrefix: '',
              sourceFiles: '${JOB_NAME}-${BUILD_NUMBER}.zip'
            ]
          ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false]
        ]
      }
    }
    stage('Deploy') {
      steps {
        sshPublisher(publishers: [
          sshPublisherDesc(configName: 'NYCUB36T', transfers: [
            sshTransfer(
              cleanRemote: false,
              excludes: '',
              execCommand: 'cd /tmp/${JOB_NAME} && unzip ${JOB_NAME}-${BUILD_NUMBER}.zip -d ${JOB_NAME}-${BUILD_NUMBER}',
              execTimeout: 120000,
              flatten: false,
              makeEmptyDirs: false,
              noDefaultExcludes: false,
              patternSeparator: '[, ]+',
              remoteDirectory: '${JOB_NAME}',
              remoteDirectorySDF: false,
              removePrefix: '',
              sourceFiles: '${JOB_NAME}-${BUILD_NUMBER}.zip')
          ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)
        ])
        sshPublisher(publishers: [
          sshPublisherDesc(configName: 'NYCUB36T', transfers: [
            sshTransfer(cleanRemote: false,
            excludes: '',
            execCommand: 'mv /tmp/${JOB_NAME}/${JOB_NAME}-${BUILD_NUMBER} /var/www/html/${JOB_NAME} && cd /var/www/html/${JOB_NAME} && sudo chgrp -R www-data storage bootstrap/cache && sudo chmod -R ug+rwx storage bootstrap/cache',
            execTimeout: 120000,
            flatten: false,
            makeEmptyDirs: false,
            noDefaultExcludes: false,
            patternSeparator: '[, ]+',
            remoteDirectory: '',
            remoteDirectorySDF: false,
            removePrefix: '',
            sourceFiles: '')
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