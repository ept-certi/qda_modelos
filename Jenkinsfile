/*******************************************************************************
 * Copyright (c) 2020 - Fundação CERTI
 * All rights reserved.
 *******************************************************************************/

pipeline {
    agent none
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    triggers {
        cron('H 03 * * *')
    }
    environment {
        GIT_DEV_BRANCH = "develop"
    }
    stages {
        stage('Models') {
            agent any
            stages {
                stage('Install requirements') {
                    steps {
                        sh 'python3 -m pip install -r requirements.txt'
                    }
                }
                stage('Lint') {
                    steps {
                        dir('src') {
                            sh 'python3 -m prospector --output-format pylint:pylint-report.txt'
                        }
                    }
                }
                stage('Tests') {
                    steps {
                        dir('src') {
                            sh 'python3 -m pytest -ra --junitxml=unittest.xml'
                        }
                    }
                }
                stage('Coverage') {
                    steps {
                        dir('src') {
                            sh 'python3 -m pytest --cov=. --cov-report=xml --cov-report=term'
                        }
                    }
                }
                stage('SonarQube') {
                    environment {
                        scannerHome = tool 'SonarQube'
                    }
                    steps {
                        script {
                            withSonarQubeEnv() {
                                sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=QDA -Dsonar.projectName=QualidadeDaAgua -Dsonar.projectBaseDir=src -Dsonar.projectVersion=1.0 -Dsonar.sources=. -Dsonar.tests=tests -Dsonar.test.inclusions=tests/test_*.py -Dsonar.python.coverage.reportPaths=coverage.xml -Dsonar.python.xunit.reportPath=unittest.xml -Dsonar.python.pylint.reportPath=pylint-report.txt'
                            }
                        }
                    }
                }
            }
        }
    }
}