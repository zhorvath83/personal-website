# My private website with resume

GitHub workflow to build docker container of my personal website with resume.

In the first phase it creates html resume files using npm package `resume-cli` which uses [standard JSON resume format](https://jsonresume.org/schema) as a basis.
In the second phase it constructs my personal website with [Hugo framework](https://gohugo.io), a static HTML and CSS website generator written in Go. 

All the generated files copied into the public folder of [nginx-unprivileged webserver](https://hub.docker.com/r/nginxinc/nginx-unprivileged) image to serve them at my private Kubernetes infrastructure.

The docker images are finally uploaded to GitHub Container registry.

[Renovate](https://github.com/renovatebot/renovate) watches this Git repository and creates pull requests when it finds updates to base Docker images and other dependencies.
