pipeline {
  agent {
    docker {
      image 'jestefane/php:7.2-fpm'
    }

  }
  stages {
    stage('Build Laravel') {
      steps {
        sh '''composer install
php -r "file_exists(\\\'.env\\\') || copy(\\\'.env.example\\\', \\\'.env\\\');"
php artisan key:generate'''
      }
    }
  }
}