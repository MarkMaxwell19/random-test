node {
    try{
        stage('build') {
            powershell("""
                [Net.ServicePointManager]::SecurityProtocol = 'tls12'
                
                function EncodeBasicAuthToken
                {
                    $Username = $ENV:GitPass.User
                    $Password = $ENV:GITPass.Password
                    $pair = '$Username:$Password'
                    $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
                    return 'Basic $encodedCreds'
                }
               
                EncodeBasicAuthToken
                $Repo = 'markmaxwell19/random-test'
                $ENV:PRNumber = 6
                Write-Host 'Hello'
                # Get current building commit
                $PRHead = (Get-PR -Repo $Repo -PR $ENV:PRNumber).head.sha
                Write-Host 'The PR head is $PRHead'
                Send-Status -Action 'Initialize' -Repo $Repo -PR $ENV:PRNumber -PRHead $PRHead -JenkinsUrl 'http://localhost:8080/job/BenTestPOC/build?token=SecretToken'

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
