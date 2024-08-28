# Credits
- https://medium.com/@nikhil.nagarajappa/configuring-the-git-credentials-in-jenkins-4b584a797b45

# setup
1. git clone thisrepo
2. cd docker-jenkins-v1
3. cd setup-docker-jenkins
4. create password file:
**.secrets/JENKINS_ADMIN_PASSWORD**
```sh
mypassword
```
5. docker-compose up

# Login
1. visit localhost:8080
- admin
- mypassword 


# Configure nodejs
- Manage Jenkins > Tools > Add NodeJs
- name: node-22
- save


# Setup credentials for github 
1. Create ssh key from within docker container - then add to github
- docker exec -it docker-jenkins bash
- ssh-keygen -t rsa -b 4096 -C myemail@sowhere.com
- enter passphrase
- cat ~/.ssh/id_rsa.pub
- cat ~/.ssh/id_rsa

2. Add ssh key into github (from public key above)

3. Add Credential to Jenkins (from private key above)
Manage Jenkins > Credentials > click - System > Add domain
- Add credential - Kind: SSH Username with private key
- ID: jenkins-testing-git
- username: docker-jenkins
- private key - Enter Directly > Add (my private key)
```
-----BEGIN OPENSS
...
E KEY-----
```

copy paste this into box
- passphrase: (mygithub pass)

# Create Job
- Creat New Item
- Name: docker-jenkins-playwright
- Freestyle project
- Ok
- Source Code Management > Git
- repo: https://github.com/inspiraller/docker-jenkins-24
- Credentials: Select docker-jenkins
Connection succesful if no red alert

- Branches to build: feature/playwright

- **Build Steps**
- Add build step
- Execute Shell
```
cd playwright
npm install
npm run test
```
- Tick - Github hook trigger for GITScm polling
- Save
# Run build manuall
- Go to Dashboard
- Click  build - docker-jenkins-playwright

# Debug
- click build - docker-jenkins-playwright
- click link - last build
- Click - console output

- error: can't cd to playwright
- Go back to build - click configure - to edit


# Create github webhook
- from same repo
- Select Branch
- Click - Settings
- Webhooks

