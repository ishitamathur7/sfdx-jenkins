pipeline {
    agent any
    environment {
        DELTA_DEPLOY_IMAGE = 'abhisheksaxena7/sfdx-git-delta:latest'
        SF_DEPLOY__ENABLED = true
        GIT_USERNAME = credentials('GIT_USERNAME')
        GIT_TOKEN = credentials('GIT_TOKEN')
        SF_ORG__QA__AUTH_URL = credentials('SF_ORG__QA__AUTH_URL')
        SF_ORG__UAT__AUTH_URL = credentials('SF_ORG__UAT__AUTH_URL')
        SF_ORG__PROD__AUTH_URL = credentials('SF_ORG__PROD__AUTH_URL')
    }
    stages {
     stage('install SFDX GIT DELTA') {
            stages{
                stage('install SGD'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "echo y | sfdx plugins:install sfdx-git-delta"
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
        }
     }
	   stage('feature') {
            when { branch 'feature/*'}
            stages{
                stage('validate_against_QA'){
                    agent {
	                    docker {
	                        image '$DELTA_DEPLOY_IMAGE'
                          alwaysPull true
	                    }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
            }
        }
        stage('QA') {
            when { branch 'QA' }
            stages{
                stage('deploy_to_QA'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
                stage('validate_against_UAT') {
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
            }
        }
        stage('master') {
            when { branch 'master' }
            stages{
                stage('deploy_to_UAT'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
                stage('validate_against_PROD') {
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
            }
        }
        stage('PROD') {
            // Ideally, This job should be triggered manually. Jenkins supports manual approval but it does not work very smoothly.
            // Either there needs to be a timeout for manual approval OR the jenkins server continues to consume resources till the time approval is provided.
            // Because of this reason, this sample triggers 'deploy to PROD' on 'v*' tag push.
            // If required, Jenkins manual approval can be used instead of tag build.
            when {
                allOf{buildingTag() ; branch 'v*'}
            }
            stages{
                stage('deploy_to_PROD'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    steps {
                        sh "sfdx --help"
                        sh "sfdx plugins"
                    }
                }
            }
        }
    }
}
