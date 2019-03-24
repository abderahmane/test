#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    gitlabCommitStatus('build') {
        stage('checkout') {
			checkout scm
		}
		stage('check java') {
			sh "java -version"
		}

		stage('clean') {
			sh "chmod +x mvnw"
			sh "./mvnw clean"
		}
		
			stage('install tools') {
			sh "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v8.12.0 -DnpmVersion=6.4.1"
		}

		stage('npm install') {
			sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm"
		}

        stage('backend tests') {
            try {
                sh "./mvnw test"
            } catch(err) {
                throw err
            } finally {
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('package and deploy') {
            sh "./mvnw com.heroku.sdk:heroku-maven-plugin:1.1.1:deploy -DskipTests -Pprod -Dheroku.appName="
            archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
        }

        stage('quality analysis') {
            withSonarQubeEnv('sonarName') {
                sh "./mvnw sonar:sonar"
            }
        }
    }
}
