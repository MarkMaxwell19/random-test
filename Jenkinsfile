node {
    try{
        stage('build') {
            powershell(""" Write-Host 'good' """)
        }
    }
    catch (e)
    {
        powershell(""" Write-host 'Failed' """)
    }
}
