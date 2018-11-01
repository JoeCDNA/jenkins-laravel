pipeline {
  agent {
    docker {
      image 'jestefane/php:7.2'
    }

  }
  stages {
    stage('Build Laravel') {
      steps {
        sh '''env
cat /etc/resolv.conf
composer install
php -r "file_exists(\\\'.env\\\') || copy(\\\'.env.example\\\', \\\'.env\\\');"
php artisan key:generate'''
      }
    }
  }
}