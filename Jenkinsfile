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
     stage('feature') {
            when {
              allOf{
                branch 'feature/*'
                changeset "force-app/**"
              }
            }
            stages{
                stage('validate_against_QA'){
                    agent {
	                    docker {
	                        image '$DELTA_DEPLOY_IMAGE'
                          alwaysPull true
	                    }
                    }
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      qa_tag = sh(script: 'git describe --match "qa-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__QA__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a QA"
                        sh "sfdx sgd:source:delta --from $qa_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $qa_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --checkonly --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --checkonly --ignorewarnings"
                    }
                }
            }
        }
        stage('QA') {
            when {
              allOf{
                branch 'QA'
                changeset "force-app/**"
              }
             }
            stages{
                stage('deploy_to_QA'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      qa_tag = sh(script: 'git describe --match "qa-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__QA__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a QA"
                        sh "sfdx sgd:source:delta --from $qa_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $qa_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --ignorewarnings"
                        sh 'git tag qa-$(date +"%Y%m%d%H%M%S")'
                        sh "git remote remove origin"
                        sh "git remote add origin 'https://$GIT_USERNAME:$GIT_TOKEN@github.com/abhisheksaxena7/sfdx-jenkins.git'"
                        sh 'git push origin --tags'
                    }
                }
                stage('validate_against_UAT') {
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      uat_tag = sh(script: 'git describe --match "uat-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__UAT__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a UAT"
                        sh "sfdx sgd:source:delta --from $uat_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $uat_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --checkonly --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --checkonly --ignorewarnings"
                    }
                }
            }
        }
        stage('master') {
            when {
              allOf{
                branch 'master'
                changeset "force-app/**"
              }
             }
            stages{
                stage('deploy_to_UAT'){
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      uat_tag = sh(script: 'git describe --match "uat-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__UAT__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a UAT"
                        sh "sfdx sgd:source:delta --from $uat_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $uat_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --ignorewarnings"
                        sh 'git tag uat-$(date +"%Y%m%d%H%M%S")'
                        sh "git remote remove origin"
                        sh "git remote add origin 'https://$GIT_USERNAME:$GIT_TOKEN@github.com/abhisheksaxena7/sfdx-jenkins.git'"
                        sh 'git push origin --tags'
                    }
                }
                stage('validate_against_PROD') {
                    agent {
                        docker {
                            image '$DELTA_DEPLOY_IMAGE'
                            alwaysPull true
                        }
                    }
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      prod_tag = sh(script: 'git describe --match "prod-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__PROD__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a PROD"
                        sh "sfdx sgd:source:delta --from $prod_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $prod_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --checkonly --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --checkonly --ignorewarnings"
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
                    environment {
                      fetch_all_tags = sh(script: 'git fetch --tags', , returnStdout: true).trim()
                      prod_tag = sh(script: 'git describe --match "prod-*" --abbrev=0 --tags HEAD', , returnStdout: true).trim()
                    }
                    steps {
                        sh "echo $SF_ORG__PROD__AUTH_URL > authURLFile"
                        sh "sfdx force:auth:sfdxurl:store -f authURLFile -s -a PROD"
                        sh "sfdx sgd:source:delta --from $prod_tag --to HEAD --output . --ignore .forceignore"
                        sh 'echo "--- package.xml generated with added and modified metadata from $prod_tag"'
                        sh "cat package/package.xml"
                        sh "sfdx force:source:deploy -x package/package.xml --testlevel NoTestRun"
                        sh 'echo "--- destructiveChanges.xml generated with deleted metadata"'
                        sh "cat destructiveChanges/destructiveChanges.xml"
                        sh "sfdx force:mdapi:deploy -d destructiveChanges --ignorewarnings"
                        sh 'git tag prod-$(date +"%Y%m%d%H%M%S")'
                        sh "git remote remove origin"
                        sh "git remote add origin 'https://$GIT_USERNAME:$GIT_TOKEN@github.com/abhisheksaxena7/sfdx-jenkins.git'"
                        sh 'git push origin --tags'
                    }
                }
            }
        }
    }
}
