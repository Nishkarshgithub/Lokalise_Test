# misc-g2-review-automation
This Repo gives you possibility to run some scripts via cron inside the docker container.

## g2_crowd_reviews.py script
This script requests the g2 reviews automatically in intercom
## Prerequisites
### Prerequisites for Automation
- Needs to be preconfigured Jenkins agent with label `customscripts`
- Create vault key value pairs for "SLACK_WEBHOOK" and "INTERCOM_TOKEN"  by the path `kv/internal/projects/customscripts/dev/g2_crowd_reviews`
- Needs to be preconfigured vault approle `jenkins-customscripts` with policy:
- Create `vault_jenkins-customscripts` credential at the jenkins master server.
- Create a job via jenkins pipeline
```
path "kv/data/internal/projects/customscripts/*" {
  capabilities = ["list","read"]
}

```
### Prerequisites for g2_crowd_reviews.py script
- Some code editor
- Python 3.9+ <br/><br/>
Check requirements.txt to see the full list of dependencies with pinned versions.
## Configuration
### Automation configuration
All scripts will be launched inside the docker container by cron. Docker container will be builded and deployed on jenkins agent with label `customscripts`.
- `services/customscripts` dir for project
- `services/customscripts/src` Directory for scripts
- `services/customscripts/build/crontab/` Directory for crontabs 
- `Jenkinsfile` Jenkins pipeline configuration
- `Dockerfile` configuration file for docker.
  
  Jenkins job will take secret from vault via jenkins vault approle credential `vault_jenkins-customscripts`. Vars "SLACK_WEBHOOK" and "INTERCOM_TOKEN" will be injected to the Jenkins agent ENV on job time, and finally they will be injected inside the docker container. 
### Configuration for g2_crowd_reviews.py script
All parameters are set via environment variables. Check out the .env file for variable examples.
- "SLACK_WEBHOOK"  Slack Webhook
- "INTERCOM_TOKEN" Intercom API token
## Usage
### Automation usage
- You need to do your changes inside the branch.
- Check your script functionality locally. Build and deploy docker container locally. Variable "ENV" is mandatory for your local testing. Please define it: `export ENV=dev`. You can use cli command `make build`, `make stop` and `make run` for build, stop and run docker container on your local machine inside the `services/customscripts` directory.
- If all above ok, then you need to create a PR. After PR creation Jenkins will automatically build and deploy new changes to the ENV

Here is info about how to add new script:
Each of the scripts have to be situated in the folder `services/customscripts/src` directory of repo.
Each of the scripts have to be declared in the crontab file for ENV. For example, here is crontab for dev env:
`services/customscripts/build/crontab/dev`
```
# This script requests the g2 reviews automatically in intercom
*/1 * * * * /bin/date >/proc/1/fd/1 2>&1 && /usr/local/bin/python /customscripts/g2_crowd_reviews.py >/proc/1/fd/1 2>&1
```
### g2_crowd_reviews.py usage
The purpose of this script is to automatically request g2 crowd reviews in intercom.


