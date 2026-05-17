# Month 2 - Docker and API Deployment

## What This Month Built

Month 2 I started with zero Docker knowledge and ended with a containerized Python REST API running as a public Docker Hub image, deployable by anyone with a single command. Along the way I covered what containers actually are at the Linux kernel level, how Docker networking works, how to build a multi container reverse proxy architecture where only one container is reachable from the outside, and how to define an entire multi container system in a single Docker Compose file. The final deliverable is a custom built Docker image containing a Flask API with a multi stage Dockerfile that reduced image size by 86.5%, from 1.62GB to 218MB.

## Run the Flask API Instantly

No setup required. Docker is the only dependency.

```bash
docker run -d -p 5000:5000 aaimarjprojects/flask-api:v2
```

Then visit `http://<host_ip>:5000/health`, you should get a 200 OK response with a health message.

## What Each Week Covers

| Week | Topic | What Was Built |
|------|-------|---------------|
| [Week 5](./week5-fundamentals/) | Docker Fundamentals & Container Theory | Understood containers at the Linux kernel level,  namespaces, cgroups, container lifecycle, and Docker networking |
| [Week 6](./week6-nginx-reverse-proxy/) | Nginx Containers & Reverse Proxy | Built a multi-container reverse proxy architecture with custom Docker networking and Docker Compose |
| [Week 7](./week7-flask-api/) | Flask API & Dockerfile | Built and containerized a Python REST API using a multi-stage Dockerfile, pushed public image to Docker Hub |

## The Story This Tells

Week 5 I established what Docker actually is beneath the surface, not just the commands to run but the Linux kernel features that make containers possible. Week 6 I applied that knowledge to build a real multi-container architecture where a reverse proxy sits in front of isolated backend services, the entire system defined in a single Compose file. Week 7 I brought everything together by writing a custom Dockerfile from scratch, building a Flask REST API, optimizing the image size by 86.5% using a multi-stage build, and shipping a public image to Docker Hub that anyone can pull and run instantly.

## Key Technical Decisions

**Multi-stage Dockerfile** - separates the build environment from the runtime environment, reducing the final image from 1.62GB to 218MB. Smaller image means faster deployment and a smaller attack surface.

**Custom bridge network** - backend containers communicate by name rather than fragile IP addresses that change on restart. Docker DNS handles resolution internally.

**No port publishing on backend containers** - the reverse proxy is the single public entry point. Backend services are invisible from outside the network, forcing all traffic through the proxy where routing rules are applied consistently.

## Docker Hub

Public image: `aaimarjprojects/flask-api:v2`
[View on Docker Hub](https://hub.docker.com/r/aaimarjprojects/flask-api)
