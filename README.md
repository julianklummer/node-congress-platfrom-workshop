# Decoupling in Practice

> **Note:**
>
> Non-Platform.sh code in this workshop comes from https://github.com/strapi/foodadvisor (MIT License 2019 Strapi Solutions)

## About

Deploying decoupled and microservice applications isn't just a problem to be solved on migration day. Moving forward with these architectures depends completely on what your team's workflow experience will look like day-to-day post-migration.

The hardest part of this can often be the number of vendors involved. Some targets are best suited for specific frontend frameworks, while others are more so for CMSs and custom APIs. Unfortunately their assumptions, workflows, APIs, and notions of security can be quite different. While there are certain advantages to relying on a strict contract between apps – where backend and frontend teams work is limited to a single vendor – this isn't always realistic. This could be because you're still experimenting, or simply the size of your organization doesn't allow for this kind of specialization just yet.

In this workshop, you'll have a chance to explore a different, single vendor approach to microservices using Strapi and Next.js as an example. You'll deploy each app individually, establishing a workflow from the start that simplifies customization, introducing new features, investigating performance issues, and even framework interchangeability from the start.

## Structure

1. A tour of Strapi: generating a backend API
2. Deploying the backend on Platform.sh
3. Deploying the frontend on Platform.sh

If there's time:

4. Switching to production services
5. Adding email
