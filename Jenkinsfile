pipeline {

    agent any

    environment {

        // Direct path to Python binary to avoid PATH inheritance issues on Windows Service agents

        PYTHON_PATH = 'C:\\Users\\HP\\AppData\\Local\\Python\\bin\\python.exe'
    }

    stages {

        stage('Checkout Source Code') {

            steps {

                checkout scm
            }
        }

        stage('Set Up Environment & Dependencies') {

            steps {

                bat '''

                @echo off

                echo [1/3] Creating virtual environment...

                if exist venv rmdir /s /q venv

                "%PYTHON_PATH%" -m venv venv

                echo [2/3] Upgrading pip...

                venv\\Scripts\\python.exe -m pip install --upgrade pip

                echo [3/3] Installing testing packages...

                venv\\Scripts\\python.exe -m pip install pytest selenium webdriver-manager

                '''
            }
        }

        stage('Execute Selenium Tests') {

            steps {

                bat '''

                @echo off

                if not exist reports mkdir reports

                echo Running Pytest Suite...

               venv\\Scripts\\python.exe -m pytest tests/ --junitxml=reports/junit-report.xml

                '''
            }
        }
    }

    post {

        always {

            // Parses test XML and renders trend graphs on the Jenkins UI

            junit testResults: 'reports/junit-report.xml', allowEmptyResults: true
        }
    }
}
