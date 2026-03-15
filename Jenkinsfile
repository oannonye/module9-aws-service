pipeline{
    agent any
        stages {
            stage('Check Commit Message') {
                steps {
                    script {
                        def commitMessage = sh(
                            script: "git log -1 --pretty=%B",
                            returnStdout: true
                        ).trim()

                        echo "Commit message: ${commitMessage}"

                        if (commitMessage.contains("[skip ci]")) {
                            echo "Skipping build because commit contains [skip ci]"
                            currentBuild.result = 'SUCCESS'
                            error("Build skipped")
                        }
                    }
                }
            }
            stage('increament version') {
                steps {
                    script {
                        dir("my-app") {
                            sh "npm version minor -no-git-tag-version"
                            def packageJson = readJSON file: 'package.json'
                            def version = packageJson.version

                            env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                        }
                    }
                }

            }
            stage('Build and Push docker image') {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', usernameVariable: 'USER', passwordVariable: 'PASS')]){
                        sh "docker build -t onyebuchia/app-store:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push onyebuchia/app-store:${IMAGE_NAME}"
                    }
                }
            }
            stage('commit version update') {
                steps {
                    script {
                        withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {

                            sh 'git remote set-url origin https://$USER:$PASS@github.com/oannonye/module9-aws-service.git'
                            sh 'git add .'
                            sh 'git commit -m "skip ci"'
                            sh 'git push origin HEAD:main'
                        }
                    }
                }
            }

        }
}