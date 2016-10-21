import org.jenkinsci.plugins.workflow.steps.FlowInterruptedException
import hudson.AbortException

node {

    echo "I'm on the ${BRANCH_NAME} branch, changes need code review."
	stage ("Question") {
    
        try {
            // stop and ask to go on
            input "Proceed?"
            
            // pretend to do some work, is  all good... 
            echo "All good..."

            // set status on the commit
            // THIS SHOULD WORK! setGitHubPullRequestStatus context:"ci-jenkins", message:"All good.", state:"SUCCESS"
            // ... DOESN'T (got bored of 'GitHub repository not configured for project?' message), SO USE:  
            step(
                [
                    $class: "GitHubCommitStatusSetter",
                    // the funky part about the context is that is "set in stone". 
                    // - the context for a branch is always continuous-integration/jenkins/branch
                    // - the context for a merge pull request (merges base) is continuous-integration/jenkins/pr-merge
                    // - the context for a head pull request (on the current branch) is continuous-integration/jenkins/pr-head
                    // WE NEED ONLY "merge"
                    contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "currently doesn't really matter"],
                    statusResultSource: 
                    [
                        $class: "ConditionalStatusResultSource", 
                        results: [[$class: "AnyBuildResult", message: "jenkins pipe succeeded", state: "SUCCESS"]]
                    ]
                ]
            )

            // some insignificant change. Stoopid comment

        }
        catch (FlowInterruptedException fie) {
            // flow interruption
            echo "Flow interruption!"
            // setGitHubPullRequestStatus context:"ci-jenkins", message:"${fie}", state:"FAILURE"
        }
        catch (AbortException ae) {
            // abort
            echo "Aborted!"
            // setGitHubPullRequestStatus context:"ci-jenkins", message:"${ae}", state:"FAILURE"
        }

    }
}
