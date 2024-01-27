# Jenkins

This tutorial shows how to deploy a simple house price prediction model.

## How-to Guide

### Start Jenkins service locally

```shell
docker compose -f docker-compose.yaml up -d
```

- Access the jenkins server at `0.0.0.0:8081`
- To get the password:

```shell
docker logs jenkins
# Jenkins initial setup is required. An admin user has been created and a password generated.
# Please use the following password to proceed to installation:
```

- Install suggested plugins & create "First Admin User" (all name with `root`)
- Install "Docker Pipeline", "Docker" plugins
  - Once login-ed, Manage Jenkins &#8594; Plugins &#8594; Available plugins &#8594; Search "Docker Pipeline" and install
- Jenkins Dashboard &#8594; "Manage Jenkins Jenkins" &#8594; "System" &#8594; Search for "GitHub API usage" and select "Never check rate limit"

### Connect Jenkins to Github

#### Publish the local Jenkins by ngrok

- [ngrok](https://ngrok.com/) is used to publish a local service (i.e. hosted on local PC), so that the outside server from Internet can connect to
  - In this case, we want to publish Jenkins server to connect with Github
  - Installation **ngrok**: `brew install ngrok/ngrok/ngrok` & follow the [guide](https://ngrok.com/docs/getting-started/)
  - Add authen-token: `Authtoken saved to configuration file: /Users/codexplore/Library/Application Support/ngrok/ngrok.yml`
- Publish the service: `ngrok http 8081` as the Jenkins is running in the port 8081
- Use forwording `https://8688-2401-7400-c80a-4079-549-77bc-e959-6c04.ngrok-free.app` path for the next step

```shell
ngrok                                                                          (Ctrl+C to quit)

Build better APIs with ngrok. Early access: ngrok.com/early-access

Session Status                online
Account                       Quan Nguyen (Plan: Free)
Version                       3.5.0
Region                        Asia Pacific (ap)
Latency                       8ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://8688-2401-7400-c80a-4079-549-77bc-e959-6c04.ngrok-free.app  -> http://localhost:8081
```

#### Add the ngrok app to Github repo's webhooks

- Access `jenkins` repo on your github account &#8594; Settings &#8594; Webhooks &#8594; Add webhook
- Pasted the URL `8688-2401-7400-c80a-4079-549-77bc-e959-6c04.ngrok-free.ap` and appending `/github-webhook/` to the Payload URL as follows:
  - Payload URL: `https://8688-2401-7400-c80a-4079-549-77bc-e959-6c04.ngrok-free.app/github-webhook/`
  - Content type: `application/json`
  - Which events would you like to trigger this webhook? &#8594; Choose "Let me select individual events." &#8594; Select "Pushes" and "Pull Requests"
  - Once done, the terminal of ngrok will appear as follows

```shell
Connections                   ttl     opn     rt1     rt5     p50     p90
                              4       0       0.00    0.01    30.12   30.18

HTTP Requests
```

#### Create a new item in Jenkins

- Jenkins Dashboard &#8594; Select "New Item" (Note: this will contain multiple projects, say `codexplore`) &#8594; Select "Multi-branch pipeline"
- Add the github repo [jenkins tutorial](https://github.com/CodexploreRepo/jenkins) as a sub-item inside `codexplore`
  - Branch Sources:
    - Add Github credentials with `id` is github `account_id` and password is the access token: "Settings" &#8594; "Developer Settings" &#8594; "Personal Access Token" &#8594; "Tokens (classics)" &#8594; Select the permissions for the token &#8594; Create & Copy the token, paste into the Jenkins's password field
    - Provide the Repository HTTPS URL to the github repo and click "Validate"
    - Save to create and the log will be appeared as follows:

```Jenkins
Started by user codexplore
[Sat Jan 27 04:16:41 UTC 2024] Starting branch indexing...
04:16:41 Connecting to https://api.github.com using CodexploreRepo/******
Examining CodexploreRepo/jenkins

  Checking branches...

  Getting remote branches...

    Checking branch main

  Getting remote pull requests...
      ‘Jenkinsfile’ not found
    Does not meet criteria

  1 branches were processed

  Checking pull-requests...

  0 pull requests were processed

Finished examining CodexploreRepo/jenkins

[Sat Jan 27 04:16:43 UTC 2024] Finished branch indexing. Indexing took 2.5 sec
Finished: SUCCESS
```

- Connect to Docker Hub:
  - Click "Credentials" on the right hand-side & click "Add Credentials"
  - User name: Docker Hub's user name
  - Password: Open "Docker Hub" &#8594; Select Account &#8594; "Security" &#8594; "New Access Token" &#8594; Give "Read, Write, Delete" &#8594; Copy the token and pasted to Password field in Jenkins
  - Id: `dockerhub`

### Push the whole code to Github for automatic deployment

```shell
git add --all
git commit -m "first attempt to deploy the model"
git push origin your_branch
```
