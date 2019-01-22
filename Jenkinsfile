node {
    // We have to checkout to a workspace with a shorted path to get around windows path name restrictions.
    // This also has a side benefit of ensuring that the worker will only use the amount of disk space
    // needed for a single build. The downside is that we can never run concurrent builds on this worker
    // and the workspaces are lost after the next build runs
    ws("C:\\wsws") {
        try {
            stage ("Clean Workspace") {
                deleteDir()
            }
	    
	    stage('Parallel Stage') {
            failFast true
            parallel {
                stage('Initialize Rest-CI') {
                    steps {
                        echo "Initialize Rest-CI"
                    }
                }
                stage('Build Code') {
                    stages {
                        stage('Cloning Sources') {
                            steps {
                                echo "Cloned Sources"
                            }
                        }
                        stage('Build') {
                            steps {
                                echo "Building"
                            }
                        }
                    }
                }
            }
        } catch (e) {
            currentBuild.result = "FAILED"
            throw e
        }
    }
}
