---
title: Self-hosting Metabase for fun and (non-)profit
slug: metabase-traefik-compose
date: 2023-07-07
tags: ["metabase", "traefik", "docker", "self-hosting", "template", "tutorial"]
---

[Reef-World](https://reef-world.org) holds 14+ years of environmental impact data collected from Green Fins assessments. This data was trapped in silos and needed human effort to manually export and transform CSV files into something meaningful.

Impact data is used by the team to understand how environmental threats have evolved over time by location and industry. With the [introduction](https://www.unep.org/news-and-stories/press-release/coral-reef-protection-grow-green-fins-hub) of [Green Fins digital membership](https://greenfins.net/digital-membership/), we needed a way to ground truth self-evaluation data and understand if Green Fins Hub engagement was having the intended behaviour centred design (BCD) impact.

I was also keen to:
- cross reference Green Fins data with publicly available ocean health datasets.
- open Reef-World's data to our stakeholder network.
- empower users to generate their own reports and data visualisations.

Enter BigQuery and Metabase.

For the uninitiated, [Metabase](https://www.metabase.com/) is an open source self-service business intelligence (BI) reporting solution. [BigQuery](https://cloud.google.com/bigquery) is Google's fully managed, serverless data warehouse. Raw data is piped into BigQuery. Metabase enables RWF Staff and trusted partners to generate their own insights by asking questions to query the raw data. These questions can then be saved as reports and used to build rich dashboards that can be shared with anyone in the Green Fins network.

I opted for an on-prem (*"manage it yourself"*) deployment of Metabase using Docker on a generic Linux virtual machine (2 vCPUs, 16 GiB memory). Postgres (with [encrypted connection information](https://www.metabase.com/docs/latest/databases/encrypting-details-at-rest)) is used for application data. [Traefik](https://traefik.io/) sits in front of Metabase to provide SSL termination (and generates [Let's Encrypt](https://letsencrypt.org/) certificates).

The template I created for this project is available on GitHub ([jamesgreenblue/metabase-traefik-compose](https://github.com/jamesgreenblue/metabase-traefik-compose)). Hopefully others in the community who are self-hosting will find this a useful starting point – especially if considering Traefik instead of Nginx.

Change is welcome so please don't hesitate to [open an issue](https://github.com/jamesgreenblue/metabase-traefik-compose/issues/new) if you spot a problem, or open a pull request for enhancements. Feedback is also welcome in this [Metabase community topic](https://discourse.metabase.com/t/production-ready-docker-compose-file-to-deploy-self-hosted-metabase-with-ssl/25882).