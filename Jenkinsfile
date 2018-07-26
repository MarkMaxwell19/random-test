node {
    withCredentials([usernameColonPassword(credentialsId: 'github-accessToken', variable: 'GITCREDS')]) {
            stage ("Clone sources") {
                    // A custom checkout is needed to increase the clone timeout:
                map_vars = checkout(
                        [
                            $class: 'GitSCM',
                            branches: scm.branches,
                            extensions: scm.extensions + [[$class: 'CloneOption', depth: 0, noTags: false, reference: '', shallow: false, timeout: 20]],
                            userRemoteConfigs: scm.userRemoteConfigs
                        ]
                    )
                Branch = map_vars.GIT_BRANCH
                LocalBranch = map_vars.GIT_LOCAL_BRANCH
                Commit = map_vars.GIT_COMMIT
            } 
            stage('build') {
                def stdout = powershell(script:'''
                    [Net.ServicePointManager]::SecurityProtocol = 'tls12'
                    Write-output "This is branch: ''' + Branch + '''"
                    Write-output "This is local: ''' + LocalBranch + '''"
                    $Repo = 'markmaxwell19/random-test'
                    $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($ENV:GITCREDS))
                    $Token = "Basic $encodedCreds"
                    $Commit = "''' + Commit + '''"

                    $Headers = @{ Authorization = $Token
                                    Accept = 'application/vnd.github.howard-the-duck-preview+json'}

                    # Send the intail response so a user can intiate a build using the Details button in GitHub.
                    $Body = (@{ state = 'pending'
                                target_url = 'http://localhost:8080/job/10.2.0%20CI%20Test/buildWithParameters?CommitNumber=' + $Commit
                                description = 'Click Details to Start build for ' + $Commit
                                context = 'continuous-integration/RESTAPI'
                                })| ConvertTo-Json

                    Write-output ("Sending initialization status for commit " + $Commit + " to https://api.github.com/repos/$Repo/statuses/" + $Commit)

                    $response = (curl -Uri ("https://api.github.com/repos/" + $Repo + "/statuses/" + $Commit) -Body $Body -Method Post -Headers $Headers -UseBasicParsing )
                ''')
                println stdout
            }
    }
}
