node('work-server') {
    try {
        stage ("Clean Workspace") {
            deleteDir()
        }
        
    } catch (e) {
        currentBuild.result = "FAILED"
        throw e
    }
}
