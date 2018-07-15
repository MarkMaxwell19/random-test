node {
    try{
        stage('build') {
            def stdout = powershell(returnStdout: true, script:'''
                [Net.ServicePointManager]::SecurityProtocol = 'tls12'
                
                $Commit = $ENV:GIT_COMMIT
                $Repo = 'markmaxwell19/random-test'
                $Username = $ENV:GitPass.User
                $Password = $ENV:GITPass.Password
                $pair = '$($Username):$($Password)'
                Write-Host $pair
                $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
                $Token = "Basic $encodedCreds"
                Write-Host $Token
    
                $Headers = @{ Authorization = $Token
                                Accept = 'application/vnd.github.howard-the-duck-preview+json'}
                
                # Send the intail response so a user can intiate a build using the Details button in GitHub.
                $Body = (@{ state = 'pending'
                            target_url = 'http://localhost:8080/job/BenTestPOC/build?token=SecretToken'
                            description = 'Click Details to Start build for $Commit'
                            context = 'continuous-integration/BenTest'
                            })| ConvertTo-Json

                Write-Host 'Sending initialization status for commit $PRHead to https://api.github.com/repos/$Repo/statuses/$Commit'

                $response = (curl -Uri 'https://api.github.com/repos/$Repo/statuses/$Commit' -Body $Body -Method Post -Headers $Headers -UseBasicParsing )
            ''')
            println stdout
        }
    }
    catch (e)
    {
        echo "hello"
    }
}
