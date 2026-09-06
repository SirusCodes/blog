---
title: "OwnDB: An attempt at democratizing database"
summary: I built OwnDB to learn how RAG systems work, ship something useful for my portfolio site, and experiment with the cost and performance trade-offs that make these systems feel production-ready.
date: 2026-09-06T00:00:00+05:30
tags: ["Tech", "PostgreSQL", "Database", "DBaaS", "Project"]
series: ["Tech", "OwnDB"]
cover:
  image: "images/cover.svg"
  alt: "OwnDB cover image"
  relative: true
---

It started with Frugality; the thought of saving some money by self-hosting Database(s). What began as a simple self-hosting automation quickly spiralled into research and cost analysis, followed by the realization that RDS is essentially a software wrapper and no open-source tool handles the setup, management, and scaling of a database without an elaborate system. This realization, paired with being at the peak of Mt. Stupid, ultimately pushed us to build what we know today as OwnDB.

# The Dream!

A minimally intrusive system that relies on nothing but SSH to be your personal Database Administrator. We wanted it to be able to run on any machine, be it a container, VM, home server, or even a smart toaster. The goal was to democratize database management and make it accessible to everyone.

# The Plan!

We decided to build a PoC and then give it to people and take their feedback on the project. We thought it would take almost a month to complete, and we were so wrong about the timelines here - this was when AI tools weren't as good as they are today. Not saying we would be successful if they were 🙃.

Before we started, we wanted to know what kind of audience we wanted to build it for and what our distribution would look like. To understand this, we shared our idea with [Omkar Khair](https://www.linkedin.com/in/omkarkhair/) and realized that a lot of people would opt for convenience, and that is the primary reason they use managed databases even if it's more economical to manage them themselves. The only way this was possible was by making sure OwnDB was on the marketplace, but that would require a good user base so that these cloud providers would let us in.

Now, with a goal in mind (get users), we started building OwnDB. While we knew what we wanted, we had to figure out how to get there. That is, decide the tools. We thought of [Ansible](https://docs.ansible.com/projects/ansible/latest/index.html), [Pulumi](https://www.pulumi.com/), etc. But just like any good engineer, we decided we needed to build the stack ourselves and started building our own automation. I guess we forgot about [xkcd's comic - Standards](https://xkcd.com/927/) and started building one ourselves.

# The Design!

The Dream was to deploy a database on a smart toaster, so how can you do that?

1. Need Internet Connection

Obviously, every smart toaster would be connected to WiFi; how else would some hacker from the other side of the world be able to burn your toast?

2. Toaster needs to have enough crunch in it

I tried looking for official hardware requirements for PostgreSQL, but there weren't any, so we needed to live with whatever the LLM hallucinated - 1 GHz CPU, 2 GB of RAM, and 512 MB of disk space.

3. Low resource utilization

The requirements of PostgreSQL are low, but we wanted to keep ours lower. So we decided to keep it agentless and only use SSH to run commands on the toaster to set up and manage PostgreSQL.

> Note - We wanted to have a minimally intrusive way to deploy a database; hence, we decided to use an agentless approach. But yes, we also wanted to run it on a smart toaster.

Since we decided to use an SSH connection, we would require the users to submit their SSH keys, and as I said, we are good engineers and we know to salt and hash passwords before storing them, we very well knew we needed to store them safely. To support open source, we used [OpenBao](https://openbao.org/) as a secrets manager - it is a fork of HashiCorp Vault. To be honest, I loved it. The docs are amazing, and how they explain everything in detail is something very rare.

{{< figure src="./images/owndb-architecture.png" title="OwnDB Architecture" >}}

Since we knew people trusted us with their SSH keys, we wanted to ensure that their keys were safe even if part of our service was compromised; no attacker would be able to access their SSH key.

With this in mind, we had divided OwnDB into two parts -

1. Dashboard - User-facing but can never talk to user machines
2. Engine (We called it Marshal) - Can talk to machines but is not open to users

So, in the worst-case scenario, even if the dashboard is compromised, it cannot read or connect to the user machine. Dashboard and Engine could only communicate via Database or a Queue.

# The Build!

We already had the architecture in mind and were able to build it successfully. It felt great to see everything fall into place and build a beautiful application.

After a few months of working on OwnDB, we were able to provision PostgreSQL on the client machine. We had also had some tests to prove that we could do it consistently.

We used [Vagrant](https://developer.hashicorp.com/vagrant) to manage virtual machines for testing Marshal, where we could consistently run a test on a fresh virtual machine instance and verify our setup.

Soon, a few decisions we made came back to bite us. The primary one was not using an existing tool to manage servers like Pulumi or Ansible. We spent a lot of time building the framework to connect to and run commands on the users' machines, instead of spending that time on building the product itself.

While building, we also found a few alternatives like [CrunchyData's postgres-operator](https://github.com/CrunchyData/postgres-operator) and [CloudNativePG](https://cloud-native-pg.io/), but they all had some downsides (or at least we thought so).

# The Launch!

After a few fights over how something should be implemented, we were all happy with where we were and decided to launch it and talk to people about it.

Before we did anything, we talked about this in [OTC CatchUp](https://catchup.ourtech.community/). [Alpesh](https://x.com/Alpastx) asked us to provision a PG on this server on Hetzner. He gave us this private key (bro trusted us), we went through the flow and realized we didn't support SSH keys with passwords, and a lot of early adopters would be savvy enough to have one. This incident made us realize how detached we were from our users.

After adding support for SSH key passwords, we planned the launch. Made a [demo video](https://www.loom.com/share/aa07839f77ae4fda8adeea46e5569e50), [pitch deck](https://docs.google.com/presentation/d/1ZtQ2tn6MReWNqqeeaarZ3ObUhf8ANKQXoNJ746oqrhE/edit?usp=sharing), and posted everywhere on social media where we could talk to people about it to get feedback.

From Reddit, we connected with a person who was really excited about it as he was building a startup and preferred to self-host everything. This was the first legitimate lead where we were talking directly to a customer - for his setup, he wanted to have backup and restore.

Then we also talked to [Vaidyanathan (Platform Engineering Leadership @ Confluent Kafka)](https://www.linkedin.com/in/vaidyanathans/) where he explained to us that for any big or mid-sized companies, [PITR (Point in Time Recovery)](https://en.wikipedia.org/wiki/Point-in-time_recovery), [HA (High Availability)](https://www.postgresql.org/docs/current/high-availability.html), alongside backup and restore, would matter a lot.

He also shared that a lot of big companies have teams who manage databases for other teams, who OwnDB might help, but it won't be something that replaces them anytime soon unless we get years of backing with high reliability.

---

Through these interviews, we realized that it is more important for us to understand our target user and build it for them, as the expectations of users are very different for every target audience. A startup might require basic features, but enterprises demand complex features with SLA guarantees.

# The Reality!

Finally, after the interview, we were in reality and realized how much more work we needed to do to make this a product.

We also realized that instead of hand-rolling our own deployment system, we should have used something like Ansible and built our application around it so we could have started with interviews much earlier. We tried to do it right instead of doing it fast for an MVP (Minimal Viable Product).

With motivation already low, we also found glaring issues with its architecture, which made it very difficult to implement HA without making a lot of changes. We could have avoided this if we had [RTFM](https://en.wikipedia.org/wiki/RTFM).

After these realizations, we had no motivation to continue working on this project and decided to sunset it.

# The Lessons!

As a proverb says, "Failure is a stepping stone to success." Though we didn't feel like continuing with the project, we have learned a lot of valuable lessons from this.

1. Do your research well. Find all the existing products and gaps within them. Only start building when you understand what exactly you are building.
2. Use all the shortcuts you can to build your MVP and get feedback. You only have so much time in hand - first build it, then build it right.
3. Decide your audience and build for them. Building for someone is easier than building for everyone. We thought we knew our audience but we didn't.

The idea had potential? Yes. Did we execute it well? No.

You can have a look at the project at [owndb.vercel.app](https://owndb.vercel.app).
