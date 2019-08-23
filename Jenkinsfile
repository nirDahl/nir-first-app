#!/usr/bin/env groovy
import java.util.regex.Matcher
import java.util.regex.Pattern

pipeline {
    
    options { 
        disableConcurrentBuilds() 
    }

    agent any

//    triggers {
//        pollSCM('H/15 * * * *')  /* every 15 minutes*/
//    }


    environment {
        ENV_NAME = "${env.BRANCH_NAME}"
        BUILD_NUM = "${env.BUILD_NUMBER}"
        BUILD_SCRIPT_PATH = "../tth-tools/Scripts"
        BUILD_CONFIG_PATH = "./Config"        
        BUILD_TYPE = "Debug"
        BUILD_TESTERS_CRASHLYTICS_GROUP = "alpha"
        APP_BUNDLE_ID = ""
        BUILD_VERSION = ""
        ANDROID_APK = ""
        iOS_IPA = ""
        PROJECT_ROOT = ""
        UPLOAD_TO = ""        
        Path = "$HOME/.rvm/rubies/ruby-2.6.3/bin:$HOME/.rvm/gems/ruby-2.6.3/bin:$HOME/.rvm/bin:$PATH"        
    }

    stages {
        stage('Build') {
            options {
                timeout(time: 60, unit: 'MINUTES')  // SECONDS, HOURS
            }

            steps {

                script {
                    
                    echo("set increment_version script permissions!")
                    sh(script: "chmod +x ./increment_version.sh", returnStatus: true)

                    echo("set quickTypeGen.sh script permissions!")
                    sh(script: "chmod +x ./quickTypeGen.sh", returnStatus: true)

                }
            }
        } // stage('Build') end

        /* run Test and Deploy in parallel */
        stage('Test') {
            options {
                timeout(time: 60, unit: 'MINUTES')  // SECONDS, HOURS
            }            
            steps {
                parallel unitTests: {
                    performTest('Test')
                }, integrationTests: {
                    performTest('IntegrationTest')
                }, failFast: false
            }
        } // stage('Test') end


        stage('Deploy-Parallel') {
            options {
                timeout(time: 60, unit: 'MINUTES')  // SECONDS, HOURS
            }
            steps {
                parallel iOS_Deploy: {
                    script {

                        echo 'iOS Deploying here'
                    }
                }, Android_Deploy: {
                    script {

                        echo 'Android Deploying here'
                    }
                },
                failFast: false
            }
        }
    }

    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run on failure'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}

// https://stackoverflow.com/questions/47646409/jenkins-groovy-regex-match-string-error-java-io-notserializableexception-jav
@NonCPS
String pullStringPatternFromSource(Pattern pattern, String sourceString) {

    def Matcher m2 = pattern.matcher(sourceString);
    if (m2.find())
        return removeLastChar(m2.group(0).split(" ")[1]);
}

// https://stackoverflow.com/questions/47646409/jenkins-groovy-regex-match-string-error-java-io-notserializableexception-jav
@NonCPS
String pullStringPatternFromSource2(Pattern pattern, String sourceString) {

    def Matcher m2 = pattern.matcher(sourceString);
    if (m2.find()) {
        String tempStr = "";
        String[] splitStrArr = m2.group(0).split(" ");
        for (int i = 1; i < splitStrArr.length; i++) {
            if (i != 1)
                tempStr += " ";

            tempStr += splitStrArr[i];
        }

        return removeLastChar(tempStr);
    }
    return "";   // if not found return empty string to avoid crash
}

@NonCPS
String removeLastChar(def str) {
    return str.substring(0, str.length() - 1);
}

void performTest(input) {

    node {
        echo 'performTest Input is ' + input
    }
}
