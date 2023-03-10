pipeline {
  agent any
  environment {
        version = "v1.0.0"
        github_org = "ajith-continuum"
        repository = "jenkins-code"
        githubToken = "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"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'a6243903-beda-4824-904e-39250299a16a', url: 'https://github.com/ajith-continuum/jenkins-code.git']]])
            }
        }
        stage('Create Git Tag') {
            steps {
                script {
                    def parts = version.tokenize(".")
                    def major = parts[0].substring(1).toInteger()
                    def minor = parts[1].toInteger()
                    def patch = parts[2].toInteger()
                    def newVersion = "v${major}.${minor}.${patch + env.BUILD_NUMBER}"
                    sh "git tag -a ${newVersion} -m 'Tag created by Jenkins build'"
                }
            }
        }
        stage('Push Git Tag') {
            steps {
                script {
                    def parts = version.tokenize(".")
                    def major = parts[0].substring(1).toInteger()
                    def minor = parts[1].toInteger()
                    def patch = parts[2].toInteger()
                    def newVersion = "v${major}.${minor}.${patch + env.BUILD_NUMBER}"
                    withCredentials([usernamePassword(credentialsId: "a6243903-beda-4824-904e-39250299a16a", passwordVariable: "githubPassword", usernameVariable: "githubUsername")]) {
                       sh "git push "https://${githubUsername}:${githubPassword}@github.com/ajith-continuum/jenkins-code.git ${newVersion}"
                    }
                }
            }
        }
        stage("Create GitHub Release") {
            steps {
                script {
                    // Retrieve the latest tag from the GitHub repository
                    def latestTag = sh(returnStdout: true, script: 'git describe --abbrev=0 --tags').trim()
                    echo "Latest tag: ${latestTag}"
                    sh "curl -X POST -H 'Authorization: token ${githubToken}' -H 'Content-Type: application/json' -d '{\"tag_name\":\"${latestTag}\",\"target_commitish\": \"main\",\"name\": \"${latestTag}\",\"body\": \"Release created by Jenkins\",\"draft\": false,\"prerelease\": false}' https://api.github.com/repos/${github_org}/${repository}/releases"       
                }
            }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t hello-world-python .'
            }
        }
        stage('Run Docker Image') {
            steps {
                sh 'docker run hello-world-python'
            }
        }
	   }
  }
