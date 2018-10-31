pipeline {
  agent {
    docker {
      image 'jestefane/php'
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