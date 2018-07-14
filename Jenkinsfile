node {
    try{
        stage('build') {
            powershell("""
                [Net.ServicePointManager]::SecurityProtocol = 'tls12'
            """)
        }
    }
    catch (e)
    {
        powershell("""
        
        Write-host 'Failed'
        """)
    }
}
