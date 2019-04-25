#CICD - Ops
Performed docker compose (+remote) via these instructions
`https://stackoverflow.com/a/53524793`

```
# Re-direct to remote environment.
export DOCKER_HOST="ssh://my-user@remote-host"

# Run your docker-compose commands.
docker-compose pull
docker-compose down
docker-compose up

# All docker-compose commands here will be run on remote-host.

# Switch back to your local environment.
unset DOCKER_HOST
```

Also, custom DNS based on a docker reverse proxy script
`https://blog.florianlopes.io/host-multiple-websites-on-single-host-docker/`

To run separate docker-compose environments, do the following
`docker-compose -p <unique_instance_name> up -d`

Dynamic CICD

Activities :

Artifactory
Jenkins
Cloud Config
EC2 - A Linux environment where we deploy containers too.
Github

At first, use a temporary local VM for demonstrating dynamic nature of 

Infrastructure Phases:
	1) Have both Client and Server projects create Dockers as part of build script
	2) Add both Client and Server projects to a Docker Compose file
	3) Create an AWS friendly Linux VM locally
		a. Install all necessary libraries (Docker, Gradle, Git)
		b. Deploy Docker Compose to VM
		c. Access app from outside of VM
	4) Configure Dockerized non-CI (Cloud Config, PostgreSQL, Artifactory, NPM JS, Docker Hub, Liquibase, Github) services on VM 
	5) Introduce Manually Kicked Off, Branch Driven Dynamic Environments to VM
		a. Add Reverse Proxy to VM
		b. Bash modify Docker Compose File
		c. Add manually dynamic building based on branch
	6) Configure Dockerized C (Jenkins) services on VM
		a. Configure to be visible to external GitHub hooks
		b. Convert Bash scripts to Jenkins
		c. Multibranch Pipeline
	7) Move VM to AWS and configure with external dependencies)

App Phases:
	1) Create ReactJS Client project that builds with NPM and Webpack
	2) Create Spring Boot RESTful Server project that's built using Gradle
	3) Connect both projects with AJAX call
	4) Add Database
	5) Add some sort of dependencies to both projects and update build scripts 
	6) Add Spring Cloud Config properties
	7) Add Artifactory properties
		a. NPM Central for JS, Artifactory for Gradle, Docker Hub for Docker.


Design Phases: 
	0) Pre-Reqs
		a. Enable Bash on Windows
		b. Write the whole thing in an executable bash script
		c. Have a reverse proxy ready
		d. Install hooks, or configs via gradle script.
	1) Static, Manual, and Local Building
		a. A single bash script will checkout both repositories 
		b. Compile each project
		c. Create a Docker of each project
		d. Run docker locally behind a reverse proxy
		e. Publish binary to NPM, Docker Hub, and Artifactory
	2) Dynamic, Manual, and Local Building
		a. Same as above but it will be driven by a branch parameter
		b. Use branch to checkout
		c. Use branch to identify Cloud Config URL
		d. Run docker behind a reverse proxy, using branch as URL
		e. Do not publish binaries
	3) Staging
		a. On feature branch merge to master perform special activities
		b. Run liquibase
		c. Merge cloud config
		d. Prevent fast forward merge
			i. https://mijingo.com/blog/preventing-non-fast-forward-git-merges
			ii. git config --global --add merge.ff false
		e. Attempt to merge branches
		f. Then rebuild the branch and deploy
		g. Prevent staging if master branch != stage branch
	4) Staging 2.0
		a. Add a meta repository project where we identify staging, plus we add releaseable notes
		b. Prevent stagign if the head of master and staging aren’t matching


TODO: 
Verify the app runs together
Remove absolute paths
Make global CLI to trigger downloading and building and running
Settup Jenkins
	Groovy script to run gradle tasks

docker exec -it --user root <jenkins_container> bash
```
aapt-get update && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   stretch \
   stable"
apt-get update && apt-get install -y docker-ce
```

```
cat /var/jenkins_home/secrets/initialAdminPassword
```

```
#guarantees that master is in the feature branch
git fetch && git merge-base --is-ancestor master HEAD && echo can fast-forward

#guarantees we have visibility of how/when a feature makes it into master. via merge commit.
git merge --no-ff
```