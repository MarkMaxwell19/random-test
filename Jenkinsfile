node {
    stage('Example') {
        if (env.BRANCH_NAME == 'master') {
            echo 'I only execute on the master branch'
        } else {
            powershell( Write-host 'I execute elsewhere')
        }
    }
}
