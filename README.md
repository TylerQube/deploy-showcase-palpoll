<div>
	<table>
		<tr>
			<td><code><img width="40" src="https://raw.githubusercontent.com/marwin1991/profile-technology-icons/refs/heads/main/icons/docker.png" alt="Docker" title="Docker"/></code></td>
			<td><code><img width="40" src="https://raw.githubusercontent.com/marwin1991/profile-technology-icons/refs/heads/main/icons/kubernetes.png" alt="Kubernetes" title="Kubernetes"/></code></td>
			<td><code><img width="40" src="https://raw.githubusercontent.com/marwin1991/profile-technology-icons/refs/heads/main/icons/nginx.png" alt="Nginx" title="Nginx"/></code></td>
			<td><code><img width="40" src="https://raw.githubusercontent.com/marwin1991/profile-technology-icons/refs/heads/main/icons/linux.png" alt="Linux" title="Linux"/></code></td>
		</tr>
	</table>
</div>

# PalPoll Deployment Pipeline
After developing my full-stack app PalPoll (an online quiz game for me and my close friends), I became frustrated with the tedious manual deployment process:
1. Push up local changes
2. SSH into my linux homelab
3. Pull the latest changes
4. Rebuild the front-end, run the back-end, and make sure the MongoDB is up

To streamline the process, and as a learning exercise in CI/CD and developer operations, I built a deployment pipeline to automatically run the full application with every push.

## Existing setup

- Ubuntu homelab hosts applications pulled from GitHub
- DNS records for subdomains configured via Cloudflare Dashboard
- Nginx reverse proxies traffic to respective processes

## CI/CD Process

### Docker
The first step in improving deployment and availability was to dockerize my existing front and backend applications.

I focused on optimizing image size through multi-stage builds using minimal base images like alpine linux

### Docker Compose
After dockerizing and testing the apps, I wrote a docker compose configuration to run the front-end, back-end, and mongodb database as a multi-container application. 

I defined this docker-compose in this repository decoupled from the application repos.

### GitHub Actions
The final piece was to automate deployment when any part of the application received a change. I'd likely limit this to manual deployment or merges in a real-world setting, but builds are fast and my commits to this project are few and far-between.

The front-end and back-end repos contain a single build-and-push workflow to build the respective image and push it to the GitHub Container Registry under my account.

After a successful push, the action then triggers a deployment workflow in this repository to:
1. SSH into a low-privilege DevOps user on my homelab
2. Pull the latest changes to the docker compose config
3. Run `docker compose up`

Nginx is manually configured to route traffic to the ports exposed by each container.

## TODO
- Run the app in a kubernetes cluster