pipeline {
    agent any
    
    environment {
        REPO_URL = 'https://github.com/venkatseetha/testing-repository'  
        BRANCH_AGE_DAYS = 30  
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                // Clone the repository
                git url: "${env.REPO_URL}", credentialsId: '3a6cb9dd-eba1-4c34-9d51-0d5fc2089095'
            }
        }
        
        stage('Delete Old Branches') {
            steps {
                script {
                    // Fetch all branches
                    sh 'git fetch --all'
                  
                    def currentDate = new Date().getTime()

                    // the list of branches
                    def branches = sh(script: 'git for-each-ref --format="%(refname:short) %(committerdate:unix)" refs/remotes/origin', returnStdout: true).trim().split('\n')

                    for (branch in branches) {
                        def parts = branch.split(' ')
                        def branchName = parts[0].replaceAll('^origin/', '')
                        def lastCommitDate = parts[1].toLong() * 1000

                        // Calculate branch age in days
                        def branchAgeDays = (currentDate - lastCommitDate) / (1000 * 60 * 60 * 24)

                        if (branchAgeDays > env.BRANCH_AGE_DAYS.toInteger() && branchName != 'main' && branchName != 'main') {
                            // Delete the branch if it is older than the specified age and not the main/master branch
                            sh "git push origin --delete ${branchName}"
                            echo "Deleted branch: ${branchName} (Age: ${branchAgeDays} days)"
                        } else {
                            echo "Skipping branch: ${branchName} (Age: ${branchAgeDays} days)"
                        }
                    }
                }
            }
        }
    }
    
    post {
        cleanup {
            // Clean up the workspace after the build
            deleteDir()
        }
    }
}
