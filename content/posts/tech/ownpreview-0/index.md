---
title: "OwnPreview #0: Why I’m Building a K8s Preview Platform"
summary: OwnPreview is an open-source, self-hosted platform for Kubernetes pull request previews. In this introduction, I break down the motivation, the architecture, and the plan to build scale-to-zero ephemeral environments from scratch.
date: 2026-11-16T00:00:00+05:30
tags: [Tech, Kubernetes, Preview, Platform Engineering, Project, K8s]
series: [Tech, OwnPreview, Kubernetes]
cover:
  image: images/cover.png
  alt: "OwnPreview #0 cover image"
  relative: true
---

Hi all, I hope every one of you has used something like this: when you give Vercel, Netlify or any other such service access to your GitHub repo, they will watch your PRs and give you a preview for that PR so you can view your changes on a deployed version, not just code. This simple feature is called [previews](https://vercel.com/products/previews) on most of the platforms.

Why? Because I want to learn a bit more about [k8s](https://kubernetes.io/) and build scale-to-zero autoscaling with [KEDA](https://keda.sh/).

Why OwnPreview? If you know, my friends and I previously built a project called [OwnDB](https://blog.darshanrander.com/posts/tech/owndb/), and the principle is the same - you self-host the solution to manage something for you. This time it's your preview environments, that's why I decided to keep it OwnPreview.

## What's The Plan?

{{< figure src="images/plan.svg" link="https://mermaid.live/edit#pako:eNptk21v2jAQx7-K5VdUAgqBPGBNSBtUMKG1iG6aVOWNSa7BwrEzP9AyxHffJdlQWZsXke_8u7v_ne0TzXQOlFELvzyoDOaCF4aXqSL4ce-08uUWTGtX3DiRiYorRxZLwi1ZCLf02_e7M2dkvf_wotYGDgJeyEwrZ7SUHyX74oXMa76GuFBgWtdH7AaKaxIdwjpzfI-uElujK48dKHBgSedFuB1Z3c0_36SqDVgse9NprZeRn7Bdar0nnfWGPFSgIL_9UeXcQX7TsjWGdKONke9GFAUK-E90izZLZFEdI2tvd2-wryUv4C12UdAO4tFnGVj77OVVWWyHkTlUUh-J0013l3ZI5zHjEnpO957A6Gu9iyXD6mUJOBPfNES0IutNC7X_e117D6iuKXOnDsJo1YQYKFE3jtKRARqVFBm3n7bmduqVE3jOpD1ijM1wb2-J2wGRQu37tEsLI3LKnPHQpSUYTIUmPdVVU4pgCSlluDSQ-9dezs2-l2mpTUpTdcZ4PMknrct_KYz2xY6yZy4tWm03fy_txWtA4dWZaZRHWRiOmySUnegrZXF_OIyiSRyEk8EoGo8nYZceKYuC_jiMkyiYJMMgjqLk3KW_m7LD_iCIB9EoDuJRnCTjMOhSyIXT5lv7dJoXdP4D4LETJA" title="The basic plan" >}}

As you can see, the plan is to keep it simple. For every PR there will be a webhook triggered for the control plane. Then the control plane will take action, like building a container image or deploying the image on the cluster.

I would also implement a way where people can directly build the image in GitHub Actions and just use OwnPreview to manage deployments. This is what [PullPreview](https://pullpreview.com/) does. In fact, they do everything from your GH Actions. While I initially considered doing the same, having a dedicated control plane unlocks opportunities to add more features.

So my plan is to build it first such that the control plane is outside of the CI pipeline and then later build a CLI as well so everything can be done from anywhere.

## Tech Stack

**Go and `client-go`**: Instead of writing bash scripts that wrap `kubectl`, I want the control plane to interact directly with the Kubernetes API. Go is the native language of K8s, making it the perfect fit for writing robust controllers.

**BuildKit**: Building container images inside a container is notoriously tricky. To do [Docker-in-Docker](https://www.docker.com/resources/docker-in-docker-containerized-ci-workflows-dockercon-2023/) requires a privileged container, which is a security risk. [BuildKit](https://docs.docker.com/build/buildkit/) is what Docker uses as the builder backend and lets us build inside a container without a privileged container.

**KEDA**: [KEDA](https://keda.sh/) is the standard in K8s for event-driven autoscaling. One of the primary things in my head was to scale-to-zero to save resources when the preview is not required.

## Difficult Road Ahead

**Concurrency**: What if a user pushes 3 commits to a PR in 2 minutes? (This is very possible in today's agentic era). We need to cancel all the previous jobs and only run the latest one.

**Cold Start**: Scaling from 0 to 1 with KEDA will introduce unavoidable latency on the first HTTP request. Handling this gracefully is crucial for the user experience.

## What's Next?

I'm planning to blog the process and share all my learnings through these posts.

In Part 1, I’ll dive into **unprivileged container image building using BuildKit**.

If you want to keep up with the progress of the project you can take a look at the [OwnPreview repo](https://github.com/SirusCodes/ownpreview) and find me on [X](https://x.com/SirusTweets) or [LinkedIn](https://linkedin.com/in/darshan-rander).
